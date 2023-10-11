
# Post \#67970273 [Link](https://stackoverflow.com/questions/67970273/)

## Inject multiple ServiceBusClient in ASP.net Core

**Vote**: 5 (499/702) **Views**: 2055 (603/702) 

**Internal ID** \#2-6-401

Created at 2021-06-14 12:31:38

Tags: `c#` `.net` `asp.net-core` `dependency-injection` `azure-web-app-service`

----------

#### Metadata:

Area: `Back-end`

Language: `c#` (full parsed tag list: `c#`)

----------

**Notepad**


----------

I want to inject multiple ServiceBusClient with same connectionstring but different queue name.
```
_services.TryAddSingleton(implementationFactory =>
    {
        var serviceBusConfiguration = implementationFactory.GetRequiredService<Microsoft.Extensions.Options.IOptions<UserEventConsumerSetting>>().Value;
    
        var serviceBusClient = new ServiceBusClient(serviceBusConfiguration.ServiceBusConnectionString, new ServiceBusClientOptions
        {
            TransportType = ServiceBusTransportType.AmqpWebSockets
        });
    
        return serviceBusClient.CreateReceiver(serviceBusConfiguration.ServiceBusQueueName, new ServiceBusReceiverOptions
        {
            ReceiveMode = ServiceBusReceiveMode.PeekLock
        });
    });
```

In order to use I have to create ServiceBusSender Instance.
```
private readonly ServiceBusSender _serviceBusSender;
    
    public CarReservationMessagingService(ServiceBusSender serviceBusSender)
    {
        _serviceBusSender = serviceBusSender ?? throw new ArgumentNullException(nameof(serviceBusSender));
    }
    
    public async Task PublishNewCarReservationMessageAsync(CarReservation carReservation)
    {
        var carReservationIntegrationMessage = new CarReservationIntegrationMessage
        {
            Id = Guid.NewGuid().ToString(),
            CarId = carReservation.CarId,
            CustomerId = carReservation.CustomerId,
            RentFrom = carReservation.RentFrom,
            RentTo = carReservation.RentTo
        };
    
        var serializedMessage = JsonSerializer.Serialize(carReservationIntegrationMessage);
        ServiceBusMessage message = new ServiceBusMessage(serializedMessage);
        await _serviceBusSender.SendMessageAsync(message);
    }
```

How can I inject multiple servicebusclient and use them differently?


----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2021-06-22 10:06:24

------------

So, In other IoC containers (like Unity) there is posibility to specify named dependencies. You specify name when regster something like this:
```
container.RegisterSingleto<SomeClassImplementation>("Name")
```

and then in class where you use is it you  should specify attribute with this name
```
[DependencyAttribute("Name")]
```

But there are no such function in .net core.
You can use Unity.
Or you can make some imitation of this functionality.
Let's create enum (or constants if you want)
```
enum ServiceBusQueue { ServiceBus1, ServiceBus2, ServiceBus3}
```

Then create service buc provider
```
public class ServiceBusQueueProvider
{
    private Dictionary<ServiceBusQueue, ServiceBusSender > _senders;

    public ServiceBusQueueProvider()
    {
        //Method to initialize all queues with all names and store it in _senders;
    }

    public ServiceBusSender Resolve(ServiceBusQueue queue)
    {
        return _senders[queue];
    }
}
```

Then register this provider and use it in other clusses that you need to get different quees:
```
_services.TryAddSingleton(implementationFactory => ServiceBusQueueProvider());
```

and in constructor:
```
public SomeClass(ServiceBusQueueProvider provider)
{
    _serviceBusSender  = provider.Resolve(ServiceBusQueue.ServiceBus1);
}
```

One more point is that you should change MessagingServiceConfiguration
This class is mapped to configuration. And in current implementation you can specify only one queue.
Change it like:
```
public class MessagingServiceConfiguration
{
    public List<MessagingServiceConfigurationItem> Queues { get; set; }
}

public class MessagingServiceConfigurationItem
{
    public string QueueName { get; set; }
    public string ListenAndSendConnectionString { get; set; }
}
```



------------
    
    
## Answer \#1

 Vote: 10

Created at 2022-03-16 08:36:08

------------

For anyone coming here in the future, I'd recommend using [ServiceBusClientBuilderExtensions](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/src/Compatibility/ServiceBusClientBuilderExtensions.cs)
```
public override void Configure(IFunctionsHostBuilder builder)
{
    builder.Services.AddAzureClients(clientsBuilder =>
    {
        clientsBuilder.AddServiceBusClient("Client1ConnectionString")
          .WithName("Client1Name");
        clientsBuilder.AddServiceBusClient("Client2ConnectionString")
          .WithName("Client2Name");
    });
}
```

In order to retrieve it you must use `IAzureClientFactory<ServiceBusClient>` as below:
```
public Constructor(IAzureClientFactory<ServiceBusClient> serviceBusClientFactory)
 {
     ServiceBusClient client1 = serviceBusClientFactory.CreateClient("Client1Name")
 }
```

Code snippets taken and simplified from [this](https://stackoverflow.com/questions/68688838/how-to-register-servicebusclient-for-dependency-injection) answer.


------------
    
    
## Answer \#2

 Vote: 1

Created at 2021-06-22 10:33:25

------------

You can create a class as below with interface. Here is a very basic example.
Interface:
```
public interface IAzureServiceBusConsumer
{
    Task Start();
    Task Stop();
}
```

Implimentation :
```
public class AzureServiceBusConsumer : IAzureServiceBusConsumer
{
    //Service Bus processor 
    private ServiceBusProcessor _userAddedProcessor;        
    private ServiceBusProcessor _userUpdatedProcessor;

    private readonly IUserReceivedService _userReceived;
    private readonly IConfiguration _configuration;       


    private readonly string _serviceBusConnectionString;
    private readonly string _subscriptionUserAdded;
    private readonly string _subscriptionUserUpdated;
    private readonly string _UserAddedMessageTopic;
    

    public AzureServiceBusConsumer(IUserReceivedService userReceived ,IConfiguration configuration)
    {
        _userReceived = userReceived;
        _configuration = configuration;          

        _serviceBusConnectionString = _configuration.GetValue<string>("ServiceBusConnectionString");
        _UserAddedMessageTopic = _configuration.GetValue<string>("UserAddedMessageTopic");
        _subscriptionUserAdded = _configuration.GetValue<string>("UserAddedSubscription");                 
       

        //Create a client to receive messages
        var client = new ServiceBusClient(_serviceBusConnectionString);

        //on client call you can provide different subscription
        _userAddedProcessor = client.CreateProcessor(_UserAddedMessageTopic, _subscriptionUserAdded);
        _userUpdatedProcessor = client.CreateProcessor(_UserAddedMessageTopic, _subscriptionUserUpdated);
    }

    /// <summary>
    /// start the bus processor to look for any messages.
    /// </summary>
    /// <returns></returns>
    public async Task Start()
    {           

        _userAddedProcessor.ProcessMessageAsync += OnUserAddedMessageReceived;
        _userAddedProcessor.ProcessErrorAsync += ErrorHandler;
        await _userAddedProcessor.StartProcessingAsync();
        _userUpdatedProcessor.ProcessMessageAsync += OnUserUpdatedMessageReceived;
        _userUpdatedProcessor.ProcessErrorAsync += ErrorHandler;
        await _userUpdatedProcessor.StartProcessingAsync();
       
    }

    /// <summary>
    /// stop the bus processor and dispose conn.
    /// </summary>
    /// <returns></returns>
    public async Task Stop()
    {
        await _userAddedProcessor.StopProcessingAsync();
        await _userAddedProcessor.DisposeAsync();
        
    }
    Task ErrorHandler(ProcessErrorEventArgs args)
    {
        Console.WriteLine(args.Exception.ToString());
        return Task.CompletedTask;
    }

    private async Task OnUserAddedMessageReceived(ProcessMessageEventArgs args)
    {
        var message = args.Message;
        var body = Encoding.UTF8.GetString(message.Body);

        UserDto _UserRecivedDto = JsonConvert.DeserializeObject<UserDto>(body);

        //Save the recieved message then call CompleteMessageAsync
        await args.CompleteMessageAsync(args.Message);
    }
```



------------
    
    