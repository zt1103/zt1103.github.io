This is a LINQ tutorial with a light focus on usefulness in small indie game dev.


What is LINQ?

Language-Integrated Query (LINQ) is the name for a set of technologies based on the integration of query capabilities directly into the C# language. Traditionally, queries against data are expressed as simple strings without type checking at compile time or IntelliSense support. Furthermore, you have to learn a different query language for each type of data source: SQL databases, XML documents, various Web services, and so on. With LINQ, a query is a first-class language construct, just like classes, methods, and events.

https://learn.microsoft.com/en-us/dotnet/csharp/linq/

Why do I need to learn LINQ?

You don't. But, a significant portion of the time, you can compress multiple lines of code down to just one.

A "quick" example:

Let's say we have a custom class to hold Cards. Cards have a value and a suit. 
```
public class Card{
	Suit suit;
	Value value;
	public override string ToString()
	{
		return $"{value} of {suit}";
	}
	public Card(Suit s, Value v){
		this.suit = s;
		this.value = v;
	}
}
public enum Suit{
	Diamond,
	Clubs,
	Hearts,
	Spades
}
public enum Value{
	Ace,
	Two,
	Three,
	Four,
	Five,
	Six,
	Seven,
	Eight,
	Nine,
	Ten,
	Jack,
	Queen,
	King
}
```


You want to create the deck. You could write out 52 lines of code, one to add each card (Ace of Spades, Two of Spades, .... King of Hearts):


(Example to come)

You could write nested loops and loop through values and suits, create cards and add them to the list:
```
public class Deck:List<Card>
{
	public Deck()
	{
		foreach (Suit s in Enum.GetValues<Suit>())
		{
			foreach (Value v in Enum.GetValues<Value>())
			{
				this.Add(new Card(s, v));
			}
		}
	}
}
```
Or using LINQ, you could:
```
public class Deck:List<Card>
{
	public Deck()
	{
		this.AddRange(Enum.GetValues<Suit>().SelectMany(x => Enum.GetValues<Value>(), (s,v) => new Card(s,v)));
	}
}
```
Now you might say "Whoa, that's a long line of code that I don't understand at all. At least I can look at the loops and understand what's happening" 

There's absolutely nothing wrong with that. 7 lines of code you understand is better than 1 line of code you don't. 

But you CAN understand the long line of code. Let's break this line of code down a little bit

```
this.AddRange(Enum.GetValues<Suit>().SelectMany(suit1 => Enum.GetValues<Value>(), (suit, value) => new Card(suit, value)));
```

```
this
```
is a keyword. We're in the constructor, so we're referring to the "Deck" that we are currently creating an instance of. Deck extends List<Card>, so really in this case we're just referring to a list

```
.AddRange(
```
AddRange is a method in System.Collections. It accepts one parameter, anything that implements IEnumerable<T>. <T> is a generic type and will be explained in more depth elsewhere
Effectively, anything inside the parentheses will be added to the referenced collection.

```
Enum.GetValues<Suit>().SelectMany(suit1 => Enum.GetValues<Value>(), (suit, value) => new Card(suit, value))
```

Here's where things get fun! (or scary). All of this together is a IEnumerable<Card> with 52 elements, one of each suit/value combination. But how does it work?

There's a few things to note in here that will make explaining this part easier. 

The first is there are two instances of 

```
Enum.GetValues<T>() 
```

one where T is Suit, and one where T is Value. This converts our Value and Suit enums into proper Enumerables that we can use LINQ functions on. 

The second thing to note is there are two lamba functions embedded into it. We can break these out if we want, but let's try not to for now. 

.SelectMany is an extension method in System.Linq. It's signature (in this case) looks like this

```
public static IEnumerable<TResult> SelectMany<TSource, TCollection, TResult>(this IEnumerable<TSource> source, Func<TSource, IEnumerable<TCollection>> collectionSelector, Func<TSource, TCollection, TResult> resultSelector)
```
We'll talk about how to read this properly in our Extension article. For now, what you need to know is if you have a IEnumerable<TSource> (a generic), and you use SelectMany on it, you can give it a Function that gives you a collection of intermediade objects (collectionSelector), and a Function that takes an item of Type TSource, an item of Type TCollection, and returns an item of TResult (resultSelector), and then it will run the function on each element of both collections and combine everything for you.

Let's apply that in real language to our example:

```
Enum.GetValues<Suit>()
```
is our IEnumerable of <Suit> that we are running SelectMany on

We have to tell it how to get from one object of type Suit (suit1) to a collection of Values, and then from one Suit and one Value, how to get to one Card.

```
suit1 => Enum.GetValues<Value>(), (suit, value) => new Card(suit, value)
```


Here's our Collection Selector Function
``` 
suit1 => Enum.GetValues<Value>()
```
Given a suit, The list of Values we need for it are all the values in our enum

Then, following our comma, we have to tell it how to get from a Suit and a Value, to a Card: The types here are inferred by the compiler, but you could also be explicit if you wanted (like on the second line)
```
 	(suit, value) => new Card(suit, value)
	(Suit suit, Value value) => new Card(suit, value)
```

```
Enum.GetValues<Suit>().SelectMany(suit1 => Enum.GetValues<Value>(), (suit, value) => new Card(suit, value))
```

Now put it all back together: 

For Each Suit in the enum Suit, get a list of the Values in the Value enum, and then, for each Suit and Value pair, create a new card

```
this.AddRange(Enum.GetValues<Suit>().SelectMany(suit1 => Enum.GetValues<Value>(), (suit, value) => new Card(suit, value)));
```

take the resulting IEnumerable<Card> and add it to the instance of this Deck object.

And that is one use of SelectMany!





















Topics to be covered in the future 

Lambda Functions and you
Generic Types 
Interfaces
Other LINQ Functions
Extension Methods more generally


