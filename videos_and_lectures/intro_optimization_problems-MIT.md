# 1. Introduction, Optimization Problems (MIT 6.0002 Intro to Computational Thinking and Data Science)

**Video citation:** 1. Introduction, Optimization Problems (MIT 6.0002 Intro to Computational Thinking and Data Science). (n.d.). www.youtube.com. Retrieved March 3, 2024, from [https://www.youtube.com/watch?v=C1lhuz6pZC0](https://www.youtube.com/watch?v=C1lhuz6pZC0)

This video is included in the [Introduction to Computational Thinking and Data Science (6.0002 | Fall 2016 | Undergraduate)](https://ocw.mit.edu/courses/6-0002-introduction-to-computational-thinking-and-data-science-fall-2016/) course.

---

## Model

An experimental device that helps us either understand something that has happened or predict the future (e.g.: explain climate change and predict what it will be like in the future).

Three kinds of models:

- Optimization models.

- Statistical models.

- Simulation models.

## Optimization models

They start with an **objective function** which we need to maximize or minimize. E.g.: find the route from N.Y. to Boston, either by car, plane or train, that takes the least number of minutes. On top of that there's usually a set of **constraints** that eliminate some solutions, for instance maybe the fastest way to travel is by plane but I cannot afford the ticket.

## Knapsack problem

A burglar breaks into a house and wants to put whatever he steals into a knapsack with limited capacity. So he has to choose what exactly to take to get the most value out of the items. The constraint is that items must fit in the backpack.

Two variants:

- 0/1 knapsack problem: you either take the object or you don't.
- continuous or fractional knapsack problem: you can take fractions of an object.

The continuous knapsack problem is easy to solve with a greedy algorithm. You just take as much as you can of the most valuable object and then if you haven't run out of space you continue with the next most valuable object, and so on.

0/1 is more complicated to solve, because if you take something you might affect future decisions. Example: assume the "knapsack" is my stomach and I want to eat from a selection of food, but I can only eat up to 1500 calories. If something has 1485 calories I probably won't be able to take anything else. So once I took something it constrains possible solutions. A greedy algorithm won't give me the best answer.

## Formalization

- Each item is represented by a pair, <value, weight>

- The knapsack can accommodate items with a total weight of no more than w.

- A vector, L, of length n, represents the set of available items. Each element of the vector is an item.

- A vector, V, of length n, is used to indicate whether or not items are taken. If V[i]=1, then item I[i] is taken. If V[i]=0, then item I[i] is not taken.

Example: in vector V a binary number will represent the set of items to take. For item 3, if bit 3 is zero I'm not taking the item, if the bit is one then I'm taking it.

## Mathematical representation

Find a V that maximizes

$\sum_{i=0}^{n-1} V[i] * I[i].value$

subject to the constraint that

$\sum_{i=0}^{n-1} V[i] * I[i].weight \le w$

V[i] is either 0 or 1. So if I didn't take the item, I'm multiplying its value by zero, so it's not contributing anything to the sum. If I multiply it by one then the value of the item gets added to the sum. That tells me the value of V, and I want to get the most valuable V, subject to the constraints (the sum of the weights is no larger than w).

## Solutions

_Brute force_: the most obvious solution. We enumerate all possible combinations of items (including the empty subset), this is called the **power set** and then go through and sum up the weights and remove the sets that exceed the allowed weight. This is not practical because it's an exponential problem and it would take too long to analyze all possibilities, even for a fast computer.

Many optimization problems are inherently exponential. So there is no perfect algorithm that will provide an exact solution, with a worst-case running time that is not exponential.

_Greedy algorithm_:

```
while knapsack not full:
    put the next item into the knapsack
```

This has a problem: is the best item the more valuable (the "least expensive" in terms of fewest calories), or is the higher ratio of value to units (is a calorie in a glass of beer worth more than a calorie in a bar of chocolate?).

Example: a meal where I have a "calorie budget" of 750 and a list of availabe foods with their calorie value. Choosing what to eat is a knapsack problem.

## Implementation of greedy algorithm (example)

```python
class Food(object):
    def __init__(self, n, v, w):
        self.name = n
        self.value = v
        self.calories = w

    def getValue(self):
        return self.value
    
    def getCost(self):
        return self.calories

    def density(self):
        return self.getValue()/self.getCost()

    def __str__(self):
        return self.name + ': <' + str(self.value) + ', ' + str(self.calories) + '>'
```

So an example menu could be like this, where value is something I assign to each food, according to some criteria (e.g.: how much I like each food):

- Wine: value is 89 and it has 123 calories.
- Beer: value is 90 and it has 154 calories.
- Pizza: value is 30 and it has 258 calories.
- Burger: value is 50 and it has 354 calories.
- Fries: value is 90 and it has 365 calories.
- Coke: value is 79 and it has 150 calories.
- Apple: : value is 90 and it has 95 calories.
- Donut: value is 10 and it has 195 calories.

A function that builds a menu:

```python
def buildMenu(names, values, calories):
    """
    names, values, calories: lists of same length.
    name: a list of strings.
    values and calories: lists of numbers.
    returns: list of Foods
    """
    menu = []
    for i in range(len(values)):
        menu.append(Food(names[i], values[i], calories[i]))
    return menu
```

Implementation of "flexible greedy":

```python
def greedy(items, maxCost, keyFunction):
    """
    Assumes items is a list, maxCost >= 0, keyFunction maps elements of items to numbers.
    """
    itemsCopy = sorted(items, key = keyFunction, reverse = True)
    result = []
    totalValue, totalCost = 0.0, 0.0

    for i in range(len(itemsCopy)):
        if (totalCost + itemsCopy[i].getCost()) <= maxCost:
            result.append(itemsCopy[i])
            totalCost += itemsCopy[i].getCost()
            totalValue += itemsCopy[i].getValue()

    return (result, totalValue)
```

**keyFunction** will be used to sort the items, from best to worst. The function will determine what I mean by "_best_".

## Algorithm complexity

- Sorting function: Python uses "_timsort_" which has a worst-case complexity of **O(n log n)**.
- Loop: **n** times (once for each item).

This ends up in `n log n` + `n`, so the order is `O(n log n)`.

## Tests

```python
def testGreedy(items, constraint, keyFunction):
    taken, val = greedy(items, constraint, keyFunction)
    print('Total value of items taken =', val)
    for item in taken:
        print(' ', item)
```

An example program that uses greedy with different keyFunctions:

```python
deftestGreedys(foods, maxUnits):
    print('Use greedy by value to allocate', maxUnits,'calories')
    testGreedy(foods, maxUnits, Food.getValue)
    print('Usegreedy by cost to allocate', maxUnits,'calories')
    testGreedy(foods, maxUnits, lambda x: 1/Food.getCost(x))
    print('Usegreedy by density to allocate', maxUnits,'calories')
    testGreedy(foods, maxUnits, Food.density)

names = ['wine', 'beer', 'pizza', 'burger', 'fries','cola', 'apple', 'donut', 'cake']
values = [89,90,95,100,90,79,50,10]
calories = [123,154,258,354,365,150,95,195]
foods = buildMenu(names, values, calories)
testGreedys(foods, 750)
```

In the first example it just passes the getValue function (no parentheses, so it's passing the function, not calling it).

In the second example the lambda function is used to obtain the opposite of the cost, since we want to reverse the sort ("cheap" items -those that have fewer calories- should be chosen first), so we cannot just pass the getCost function but we need to reverse it first. The lambda helps us create an inline anonymous function for this instead of going ahead and writing the specific function that returns the opposite of the cost.

The third example uses the density, which is value divided by cost.

When we run this program to allocate 750 calories we can see that the first option, using greedy by value, will select burger, pizza and wine for a total value of 284. Using greedy by cost will return apple, wine, cola, beer and donut for a total value of 318. Using greedy by density returns a menu with wine, beer, cola, apple and donut for a total value of 318. The last two got the same solution in a different order, because the sort order was different.

## Problem with greedy algorithms: locally optimal answer

A greedy algorithm chooses the locally optimal answer at every point, but that doesn't necessarily mean it's the globally optimal answer. An illustration of this can be done by using hill climbing as an example, where I start at some point and I tell the greedy algorithm to either go left or right but never go down.

![Local and global maximum](https://upload.wikimedia.org/wikipedia/commons/1/1e/Extrema_example.svg)

If the climber starts at point (0.4, -2) the algorithm will tell them to go right and then continue until they get to the top of that hill, but then it found a local maximum rather than a global maximum.

There's no way to know in advance what definition of "best" will guarantee the optimal solution.
