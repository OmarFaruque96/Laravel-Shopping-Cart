# Laravel-Shopping-Cart
step by step description / guide line for shopping cart
# Laravel-Package
# Link: https://github.com/bumbummen99/LaravelShoppingcart

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class CartController extends Controller
{
    public function cartList()
    {
        $cartItems = \Cart::getContent();
        // dd($cartItems);
        return view('cart', compact('cartItems'));
    }


    public function addToCart(Request $request)
    {
        \Cart::add([
            'id' => $request->id,
            'name' => $request->name,
            'price' => $request->price,
            'quantity' => $request->quantity,
            'attributes' => array(
                'image' => $request->image,
            )
        ]);
        session()->flash('success', 'Product is Added to Cart Successfully !');

        return redirect()->route('cart.list');
    }

    public function updateCart(Request $request)
    {
        \Cart::update(
            $request->id,
            [
                'quantity' => [
                    'relative' => false,
                    'value' => $request->quantity
                ],
            ]
        );

        session()->flash('success', 'Item Cart is Updated Successfully !');

        return redirect()->route('cart.list');
    }

    public function removeCart(Request $request)
    {
        \Cart::remove($request->id);
        session()->flash('success', 'Item Cart Remove Successfully !');

        return redirect()->route('cart.list');
    }

    public function clearAllCart()
    {
        \Cart::clear();

        session()->flash('success', 'All Item Cart Clear Successfully !');

        return redirect()->route('cart.list');
    }
}

# Details 
Installation
Install the package through Composer.

Run the Composer require command from the Terminal:

composer require bumbummen99/shoppingcart
Now you're ready to start using the shoppingcart in your application.

As of version 2 of this package it's possibly to use dependency injection to inject an instance of the Cart class into your controller or other class

You definitely should publish the config file and take a look at it.

php artisan vendor:publish --provider="Gloudemans\Shoppingcart\ShoppingcartServiceProvider" --tag="config"
This will give you a cart.php config file in which you can make changes to the packages behaivor.

Table of Contents
Look at one of the following topics to learn more about LaravelShoppingcart

Important note
Usage
Collections
Instances
Models
Database
Calculators
Exceptions
Events
Example
Contributors
Important note
As all the shopping cart that calculate prices including taxes and discount, also this module could be affected by the "totals rounding issue" (*) due to the decimal precision used for prices and for the results. In order to avoid (or at least minimize) this issue, in the Laravel shoppingcart package the totals are calculated using the method "per Row" and returned already rounded based on the number format set as default in the config file (cart.php). Due to this WE DISCOURAGE TO SET HIGH PRECISION AS DEFAULT AND TO FORMAT THE OUTPUT RESULT USING LESS DECIMAL Doing this can lead to the rounding issue.

The base price (product price) is left not rounded.

Usage
The shoppingcart gives you the following methods to use:

Cart::add()
Adding an item to the cart is really simple, you just use the add() method, which accepts a variety of parameters.

In its most basic form you can specify the id, name, quantity, price and weight of the product you'd like to add to the cart.

Cart::add('293ad', 'Product 1', 1, 9.99, 550);
As an optional fifth parameter you can pass it options, so you can add multiple items with the same id, but with (for instance) a different size.

Cart::add('293ad', 'Product 1', 1, 9.99, 550, ['size' => 'large']);
The add() method will return an CartItem instance of the item you just added to the cart.

Maybe you prefer to add the item using an array? As long as the array contains the required keys, you can pass it to the method. The options key is optional.

Cart::add(['id' => '293ad', 'name' => 'Product 1', 'qty' => 1, 'price' => 9.99, 'weight' => 550, 'options' => ['size' => 'large']]);
New in version 2 of the package is the possibility to work with the Buyable interface. The way this works is that you have a model implement the Buyable interface, which will make you implement a few methods so the package knows how to get the id, name and price from your model. This way you can just pass the add() method a model and the quantity and it will automatically add it to the cart.

As an added bonus it will automatically associate the model with the CartItem

Cart::add($product, 1, ['size' => 'large']);
As an optional third parameter you can add options.

Cart::add($product, 1, ['size' => 'large']);
Finally, you can also add multipe items to the cart at once. You can just pass the add() method an array of arrays, or an array of Buyables and they will be added to the cart.

When adding multiple items to the cart, the add() method will return an array of CartItems.

Cart::add([
  ['id' => '293ad', 'name' => 'Product 1', 'qty' => 1, 'price' => 10.00, 'weight' => 550],
  ['id' => '4832k', 'name' => 'Product 2', 'qty' => 1, 'price' => 10.00, 'weight' => 550, 'options' => ['size' => 'large']]
]);

Cart::add([$product1, $product2]);
Cart::update()
To update an item in the cart, you'll first need the rowId of the item. Next you can use the update() method to update it.

If you simply want to update the quantity, you'll pass the update method the rowId and the new quantity:

$rowId = 'da39a3ee5e6b4b0d3255bfef95601890afd80709';

Cart::update($rowId, 2); // Will update the quantity
If you would like to update options of an item inside the cart,

$rowId = 'da39a3ee5e6b4b0d3255bfef95601890afd80709';

Cart::update($rowId, ['options'  => ['size' => 'small']]); // Will update the size option with new value
If you want to update more attributes of the item, you can either pass the update method an array or a Buyable as the second parameter. This way you can update all information of the item with the given rowId.

Cart::update($rowId, ['name' => 'Product 1']); // Will update the name

Cart::update($rowId, $product); // Will update the id, name and price
Cart::remove()
To remove an item for the cart, you'll again need the rowId. This rowId you simply pass to the remove() method and it will remove the item from the cart.

$rowId = 'da39a3ee5e6b4b0d3255bfef95601890afd80709';

Cart::remove($rowId);
Cart::get()
If you want to get an item from the cart using its rowId, you can simply call the get() method on the cart and pass it the rowId.

$rowId = 'da39a3ee5e6b4b0d3255bfef95601890afd80709';

Cart::get($rowId);
Cart::content()
Of course you also want to get the carts content. This is where you'll use the content method. This method will return a Collection of CartItems which you can iterate over and show the content to your customers.

Cart::content();
This method will return the content of the current cart instance, if you want the content of another instance, simply chain the calls.

Cart::instance('wishlist')->content();
Cart::destroy()
If you want to completely remove the content of a cart, you can call the destroy method on the cart. This will remove all CartItems from the cart for the current cart instance.

Cart::destroy();
Cart::weight()
The weight() method can be used to get the weight total of all items in the cart, given their weight and quantity.

Cart::weight();
The method will automatically format the result, which you can tweak using the three optional parameters

Cart::weight($decimals, $decimalSeperator, $thousandSeperator);
You can set the default number format in the config file.

If you're not using the Facade, but use dependency injection in your (for instance) Controller, you can also simply get the total property $cart->weight

Cart::total()
The total() method can be used to get the calculated total of all items in the cart, given there price and quantity.

Cart::total();
The method will automatically format the result, which you can tweak using the three optional parameters

Cart::total($decimals, $decimalSeparator, $thousandSeparator);
You can set the default number format in the config file.

If you're not using the Facade, but use dependency injection in your (for instance) Controller, you can also simply get the total property $cart->total

Cart::tax()
The tax() method can be used to get the calculated amount of tax for all items in the cart, given there price and quantity.

Cart::tax();
The method will automatically format the result, which you can tweak using the three optional parameters

Cart::tax($decimals, $decimalSeparator, $thousandSeparator);
You can set the default number format in the config file.

If you're not using the Facade, but use dependency injection in your (for instance) Controller, you can also simply get the tax property $cart->tax

Cart::subtotal()
The subtotal() method can be used to get the total of all items in the cart, minus the total amount of tax.

Cart::subtotal();
The method will automatically format the result, which you can tweak using the three optional parameters

Cart::subtotal($decimals, $decimalSeparator, $thousandSeparator);
You can set the default number format in the config file.

If you're not using the Facade, but use dependency injection in your (for instance) Controller, you can also simply get the subtotal property $cart->subtotal

Cart::discount()
The discount() method can be used to get the total discount of all items in the cart.

Cart::discount();
The method will automatically format the result, which you can tweak using the three optional parameters

Cart::discount($decimals, $decimalSeparator, $thousandSeparator);
You can set the default number format in the config file.

If you're not using the Facade, but use dependency injection in your (for instance) Controller, you can also simply get the subtotal property $cart->discount

Cart::initial()
The initial() method can be used to get the total price of all items in the cart before applying discount and taxes.

It could be deprecated in the future. When rounded could be affected by the rounding issue, use it carefully or use Cart::priceTotal()

Cart::initial();
The method will automatically format the result, which you can tweak using the three optional parameters.

Cart::initial($decimals, $decimalSeparator, $thousandSeparator);
You can set the default number format in the config file.

Cart::priceTotal()
The priceTotal() method can be used to get the total price of all items in the cart before applying discount and taxes.

Cart::priceTotal();
The method return the result rounded based on the default number format, but you can tweak using the three optional parameters

Cart::priceTotal($decimals, $decimalSeparator, $thousandSeparator);
You can set the default number format in the config file.

If you're not using the Facade, but use dependency injection in your (for instance) Controller, you can also simply get the subtotal property $cart->initial

Cart::count()
If you want to know how many items there are in your cart, you can use the count() method. This method will return the total number of items in the cart. So if you've added 2 books and 1 shirt, it will return 3 items.

Cart::count();
$cart->count();
Cart::search()
To find an item in the cart, you can use the search() method.

This method was changed on version 2

Behind the scenes, the method simply uses the filter method of the Laravel Collection class. This means you must pass it a Closure in which you'll specify you search terms.

If you for instance want to find all items with an id of 1:

$cart->search(function ($cartItem, $rowId) {
	return $cartItem->id === 1;
});
As you can see the Closure will receive two parameters. The first is the CartItem to perform the check against. The second parameter is the rowId of this CartItem.

The method will return a Collection containing all CartItems that where found

This way of searching gives you total control over the search process and gives you the ability to create very precise and specific searches.

Cart::setTax($rowId, $taxRate)
You can use the setTax() method to change the tax rate that applies to the CartItem. This will overwrite the value set in the config file.

Cart::setTax($rowId, 21);
$cart->setTax($rowId, 21);
Cart::setGlobalTax($taxRate)
You can use the setGlobalTax() method to change the tax rate for all items in the cart. New items will receive the setGlobalTax as well.

Cart::setGlobalTax(21);
$cart->setGlobalTax(21);
Cart::setGlobalDiscount($discountRate)
You can use the setGlobalDiscount() method to change the discount rate for all items in the cart. New items will receive the discount as well.

Cart::setGlobalDiscount(50);
$cart->setGlobalDiscount(50);
Cart::setDiscount($rowId, $taxRate)

# You can use the setDiscount() method to change the discount rate that applies a CartItem. Keep in mind that this value will be changed if you set the global discount for the Cart afterwards.

Cart::setDiscount($rowId, 21);
$cart->setDiscount($rowId, 21);
