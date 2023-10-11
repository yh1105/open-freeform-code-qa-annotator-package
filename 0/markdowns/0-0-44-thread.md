
# Post \#65421526 [Link](https://stackoverflow.com/questions/65421526/)

## Nest.js - Create index in mongoose schema

**Vote**: 4 (532/702) **Views**: 8706 (442/702) 

**Internal ID** \#0-0-44

Created at 2020-12-23 08:58:02

Tags: `node.js` `mongoose` `nestjs`

----------

#### Metadata:

Area: `Front-end`

Language: `javascript` (full parsed tag list: `javascript`)

----------

**Notepad**


----------

How do I create an index of property in mongoose schema using Nest.js?
I tried to add index as a property option, But the index hasn't been created:
```
@Schema()
export class Schema extends Document {

  @Prop()
  _id: string;

  @Prop({required: true, index: true})
  type: string;

  @Prop()
  creationDate: string;

  @Prop()
  name: string;
}

export const MySchema = SchemaFactory.createForClass(Schema);
```

I tried this way too:
```
export const MySchema = SchemaFactory.createForClass(Schema).index({ type: 1 });
```

Both doesn't work as expected.
What is the way to do that?
Thanks


----------
        
## Answer \#0

**Accepted** Vote: 10

Created at 2020-12-24 09:30:40

------------

Use following option to create index
```
@Schema({useCreateIndex: true})
    export class Schema extends Document {
    
      @Prop()
      _id: string;
    
      @Prop({required: true, index: true})
      type: string;
    
      @Prop()
      creationDate: string;
    
      @Prop()
      name: string;
    }

export const MySchema = SchemaFactory.createForClass(Schema);
```

use `useCreateIndex` flag either when defining the schema
or globally set the same flag when creating the connection object
```
{
  uri: `....`,
  user: ,
  pass: ,
  //useNewUrlParser: true,
  useCreateIndex: true,
  //useUnifiedTopology: true,
  //useFindAndModify: false,
  retryAttempts: 3
}
```

Added other commented flags as well which could be required.


------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-02-21 15:05:46

------------

This works for me
```
export const MySchema = SchemaFactory.createForClass(Schema);
MySchema.index({ type: 1 }, { unique: true });
```

Same can be extended for compound index as well - ex:
```
MySchema.index({ type: 1, name: 1 }, { unique: true });
```



------------
    
    
## Answer \#2

 Vote: 5

Created at 2021-05-30 08:25:49

------------

If someone came here looking for how to add index for a geo location/2dsphere, just like me, You can use the following in NestJs.
```
@Prop({ index: "2dsphere" })
```

Also when you are setting up the mongoose module, add `useCreateIndex: true`.
```
MongooseModule.forRootAsync({
 useFactory: async (config: ConfigService) => ({
   uri: config.get('mongo_url'),
   useNewUrlParser: true,
   useCreateIndex: true,
 }),
 inject: [ConfigService],
})
```



------------
    
    