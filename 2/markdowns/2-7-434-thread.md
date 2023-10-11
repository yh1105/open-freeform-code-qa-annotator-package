
# Post \#70211690 [Link](https://stackoverflow.com/questions/70211690/)

## New alternative for getDoctrine() in Symfony 5.4 and up

**Vote**: 15 (318/702) **Views**: 15544 (352/702) 

**Internal ID** \#2-7-434

Created at 2021-12-03 08:54:01

Tags: `php` `symfony` `doctrine` `symfony5`

----------

#### Metadata:

Area: `Back-end`

Language: `php` (full parsed tag list: `php`)

----------

**Notepad**


----------

As my IDE points out, the `AbstractController::getDoctrine()` method is now deprecated.
I haven't found any reference for this deprecation neither in the official documentation nor in the Github changelog.
What is the new alternative or workaround for this shortcut?


----------
        
## Answer \#0

**Accepted** Vote: 32

Created at 2021-12-03 09:05:31

------------

As mentioned [here](https://symfony.com/blog/new-in-symfony-5-4-controller-changes):
> Instead of using those shortcuts, inject the related services in the constructor or the controller methods.
You need to use dependency injection.
For a given controller, simply inject `ManagerRegistry` on the controller's constructor.
```
use Doctrine\Persistence\ManagerRegistry;

class SomeController {

    public function __construct(private ManagerRegistry $doctrine) {}

    public function someAction(Request $request) {
        // access Doctrine
        $this->doctrine;
    }
}
```



------------
    
    
## Answer \#1

 Vote: 5

Created at 2021-12-07 13:56:39

------------

You can use EntityManagerInterface $entityManager:
```
public function delete(Request $request, Test $test, EntityManagerInterface $entityManager): Response
{
    if ($this->isCsrfTokenValid('delete'.$test->getId(), $request->request->get('_token'))) {
        $entityManager->remove($test);
        $entityManager->flush();
    }

    return $this->redirectToRoute('test_index', [], Response::HTTP_SEE_OTHER);
}
```



------------
    
    
## Answer \#2

 Vote: 3

Created at 2022-02-17 09:53:09

------------

As per the answer of @yivi and as mentionned in the [documentation](https://symfony.com/doc/current/doctrine.html#persisting-objects-to-the-database), you can also follow the example below by injecting `Doctrine\Persistence\ManagerRegistry` directly in the method you want:
```
// src/Controller/ProductController.php
namespace App\Controller;

// ...
use App\Entity\Product;
use Doctrine\Persistence\ManagerRegistry;
use Symfony\Component\HttpFoundation\Response;

class ProductController extends AbstractController
{
    /**
     * @Route("/product", name="create_product")
     */
    public function createProduct(ManagerRegistry $doctrine): Response
    {
        $entityManager = $doctrine->getManager();

        $product = new Product();
        $product->setName('Keyboard');
        $product->setPrice(1999);
        $product->setDescription('Ergonomic and stylish!');

        // tell Doctrine you want to (eventually) save the Product (no queries yet)
        $entityManager->persist($product);

        // actually executes the queries (i.e. the INSERT query)
        $entityManager->flush();

        return new Response('Saved new product with id '.$product->getId());
    }
}
```



------------
    
    
## Answer \#3

 Vote: 2

Created at 2022-09-19 01:24:32

------------

Add code in controller, and not change logic the controller
```
<?php
//...
use Doctrine\Persistence\ManagerRegistry;
//...
class AlsoController extends AbstractController
{
    public static function getSubscribedServices(): array
    {
        return array_merge(parent::getSubscribedServices(), [
            'doctrine' => '?'.ManagerRegistry::class,
        ]);
    }
    protected function getDoctrine(): ManagerRegistry
    {
        if (!$this->container->has('doctrine')) {
            throw new \LogicException('The DoctrineBundle is not registered in your application. Try running "composer require symfony/orm-pack".');
        }
        return $this->container->get('doctrine');
    }
...
}
```

read more [https://symfony.com/doc/current/service_container/service_subscribers_locators.html#including-services](https://symfony.com/doc/current/service_container/service_subscribers_locators.html#including-services)


------------
    
    
## Answer \#4

 Vote: -1

Created at 2022-04-20 20:34:32

------------

In my case, relying on constructor- or method-based autowiring is not flexible enough.
I have a trait used by a number of Controllers that define their own autowiring. The trait provides a method that fetches some numbers from the database. I didn't want to tightly couple the trait's functionality with the controller's autowiring setup.
I created yet another trait that I can include anywhere I need to get access to Doctrine. The bonus part? It's still a legit autowiring approach:
```
<?php

namespace App\Controller;

use Doctrine\Persistence\ManagerRegistry;
use Doctrine\Persistence\ObjectManager;
use Symfony\Contracts\Service\Attribute\Required;

trait EntityManagerTrait
{
    protected readonly ManagerRegistry $managerRegistry;

    #[Required]
    public function setManagerRegistry(ManagerRegistry $managerRegistry): void
    {
        // @phpstan-ignore-next-line PHPStan complains that the readonly property is assigned outside of the constructor.
        $this->managerRegistry = $managerRegistry;
    }

    protected function getDoctrine(?string $name = null, ?string $forClass = null): ObjectManager
    {
        if ($forClass) {
            return $this->managerRegistry->getManagerForClass($forClass);
        }

        return $this->managerRegistry->getManager($name);
    }
}
```

and then
```
<?php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use App\Entity\Foobar;

class SomeController extends AbstractController
{
    use EntityManagerTrait

    public function someAction()
    {
        $result = $this->getDoctrine()->getRepository(Foobar::class)->doSomething();
        // ...
    }
}
```

If you have multiple managers like I do, you can use the `getDoctrine()` arguments to fetch the right one too.


------------
    
    