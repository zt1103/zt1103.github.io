This is a LINQ tutorial with a light focus on usefulness in small indie game dev.


What is LINQ?

Language-Integrated Query (LINQ) is the name for a set of technologies based on the integration of query capabilities directly into the C# language. Traditionally, queries against data are expressed as simple strings without type checking at compile time or IntelliSense support. Furthermore, you have to learn a different query language for each type of data source: SQL databases, XML documents, various Web services, and so on. With LINQ, a query is a first-class language construct, just like classes, methods, and events.

https://learn.microsoft.com/en-us/dotnet/csharp/linq/

Why do I need to learn LINQ?

You don't. But, a significant portion of the time, you can compress multiple lines of code down to just one.

A quick example:

Let's say we have a custom class to hold Cards. Cards have a value and a suit. 
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



You want to create the deck. You could write out 52 lines of code, one to add each card (Ace of Spades, Two of Spades, .... King of Hearts):


(Example to come)

You could write nested loops and loop through values and suits, create cards and add them to the list:
public class Deck:List<Card>
{
	public Deck()
	{
    foreach (Suit s in Enum.GetValues(typeof(Suit)))
		{
			foreach (Value v in Enum.GetValues(typeof(Value)))
			{
				this.Add(new Card(s,v));
			}
		}
  }
}
Or using LINQ, you could:

