---
layout: post
title: "Clean Code Correction"
date: 2017-8-5 16:25:06
description: Dissecting an example from clean code and suggesting an alternative solution
tags: 
- clean-code
- craftsmanship
- object-orientation
- object-oriented-programming
---
I was reading Clean Code by Uncle Bob (long overdue, I know) and I came across this example on page 29 of my edition. Chapter 2.

```
public class GuessStatisticsMessage {
    private String number;
    private String verb;
    private String pluralModifier;

    public String make(char candidate, int count) {
        createPluralDependentMessageParts(count);
        return String.format(
            "There %s %s %s%s",
             verb, number, candidate, pluralModifier );
    }

    private void createPluralDependentMessageParts(int count) {
        if (count == 0) {
            thereAreNoLetters();
        } else if (count == 1) {
            thereIsOneLetter();
        } else {
            thereAreManyLetters(count);
        }
    }
    
    private void thereAreManyLetters(int count) {
        number = Integer.toString(count);
        verb = "are";
        pluralModifier = "s";
    }
    
    private void thereIsOneLetter() {
        number = "1";
        verb = "is";
        pluralModifier = "";
    }
    
    private void thereAreNoLetters() {
        number = "no";
        verb = "are";
        pluralModifier = "s";
    }
}
```

I'm not a fool. I'm hesitant to call out Uncle Bob on software craftsmanship... but something about this class ticked me wrong. I don't like that the class has a mutable state mixed in with passing in arbitrary parameters to build the message on.  

I tried this implementation because it makes the class immutable. It is a semantic change though. Now the class is no longer a message builder, it is an instance of a message that can be converted into a string. My issue with the new implementation is that it has a lot of state to maintain. That is however countered by the fact that the critical pieces of state, the count and the candidate are immutable. If we can take it as read that  

```
createPluralDependentMessageParts()
```

is an idempotent function then we know that the rest of the parameters are effectively immutable once set. I would much prefer if I could write:  

```
verb, number, candidate = createPluralDependentMessageParts();
```

and not store those on the class but Java doesn't allow that and I can't think of a suitable encapsulation for those items.  

```
public class StatisticsMessage {
    private String number;
    private String verb;
    private String pluralModifier;
    private char candidate;
    private int count;
    
    public StatisticsMessage(char candidate, int count){
        this.candidate = candidate;
        this.count = count;
    }
    
    public String toString() {
        createPluralDependentMessageParts();
        return String.format(
            "There %s %s %s%s",
             verb, number, candidate, pluralModifier );
    }

    private void createPluralDependentMessageParts() {
        if (count == 0) {
            thereAreNoLetters();
        } else if (count == 1) {
            thereIsOneLetter();
        } else {
            thereAreManyLetters();
        }
    }
    
    private void thereAreManyLetters() {
        number = Integer.toString(count);
        verb = "are";
        pluralModifier = "s";
    }
    
    private void thereIsOneLetter() {
        number = "1";
        verb = "is";
        pluralModifier = "";
    }
    
    private void thereAreNoLetters() {
        number = "no";
        verb = "are";
        pluralModifier = "s";
    }
}
```
This may be a bit too C-Sharpey... (I'm not confident of the common patterns in Java) so I've just translated it into C# where I have more powerful tools (and a greater depth of knowledge) to enforce the immutability.  

```
public class StatisticsMessage
{
    private string _number;
    private string _verb;
    private string _pluralModifier;
    private readonly char _candidate;
    private readonly int _count;

    public StatisticsMessage(char candidate, int count)
    {
        _candidate = candidate;
        _count = count;
    }

    public override string ToString()
    {
        CreatePluralDependentMessageParts();
        return $"There {_verb} {_number} {_candidate}{_pluralModifier}";
    }

    private void CreatePluralDependentMessageParts()
    {
        if (_count == 0)
        {
            ThereAreNoLetters();
            return;
        }
        if (_count == 1)
        {
            ThereIsOneLetter();
            return;
        }
        ThereAreManyLetters();
    }

    private void ThereAreManyLetters()
    {
        _number = _count.ToString();
        _verb = "are";
        _pluralModifier = "s";
    }

    private void ThereIsOneLetter()
    {
        _number = "1";
        _verb = "is";
        _pluralModifier = "";
    }

    private void ThereAreNoLetters()
    {
        _number = "no";
        _verb = "are";
        _pluralModifier = "s";
    }
}
```
