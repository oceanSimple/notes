# ♾️介绍
本项目旨在解决开发德州扑克类项目的工具包.
实现功能如下:
- 扑克牌-Card, 牌盒-Deck, 手牌-Hand
- Deck
	- 随机打乱
	- 排序
	- 抽牌
- 手牌比较: 按照德州扑克的规则, 进行手牌大小的比较

# ♾️使用示例
```csharp
using Poker;

var handler = PokerHandler.Instance;

// 获得一副牌
var deck = handler.GetDeck();
// 洗牌
deck.Shuffle();

// 各抽七张牌
var card1 = deck.Draw(7);
var card2 = deck.Draw(7);

Console.WriteLine($"Card1: {string.Join(" ", card1)}");
Console.WriteLine($"Card2: {string.Join(" ", card2)}");

// 通过手上的七张牌, 获得最大的五张牌
var hand1 = handler.GetHand(card1);
var hand2 = handler.GetHand(card2);

Console.WriteLine($"Hand1: {hand1}");
Console.WriteLine($"Hand2: {hand2}");

// 比较大小
var result = handler.CompareTwoHands(hand1, hand2);
Console.WriteLine($"Compare result: {result}");
```

> 运行结果(随机)

```shell
Card1: ♣J ♣3 ♦8 ♥7 ♠J ♦J ♦3
Card2: ♣4 ♥2 ♠8 ♠4 ♥8 ♠3 ♠2
Hand1: ♣J ♠J ♦J ♣3 ♦3
Hand2: ♠8 ♥8 ♣4 ♠4 ♠3
Compare result: 1
```



# ♾️Api 接口
```csharp
public interface IHandler {
    /// <summary>
    ///     Compare two hands. <br />
    /// </summary>
    /// <returns>
    ///     1: hand1 > hand2 <br />
    ///     0: hand1 = hand2 <br />
    ///     -1: hand1 less than  hand2  <br />
    /// </returns>
    int CompareTwoHands(Hand hand1, Hand hand2);

    /// <summary>
    ///     Get the max hands' index from the given hands. <br />
    /// </summary>
    /// <returns>
    ///     A list of max hands' index from the given hands. <br />
    /// </returns>
    List<int> GetMaxHands(List<Hand> hands);

    /// <summary>
    ///     Sorts the cards by their number in descending order.
    /// </summary>
    /// <param name="cards">The cards that need to be sorted</param>
    void SortCardsByNumber(List<Card> cards);

    /// <summary>
    ///     Sorts the cards by their color in ascending order. <br />
    ///     If two cards have the same color, they are sorted by their number in descending order.
    /// </summary>
    /// <param name="cards">The cards that need to be sorted</param>
    void SortCardsByColor(List<Card> cards);

    /// <summary>
    ///     Get the deck of cards. <br />
    /// </summary>
    /// <returns></returns>
    Deck GetDeck();

    /// <summary>
    ///     Get the card by its color and number. <br />
    /// </summary>
    /// <returns></returns>
    Card GetCard(EColor color, ENumber number);

	/// <summary>
    ///     Get the hand from the given cards. <br />
    /// </summary>
    /// <returns></returns>
    Hand GetHand(List<Card> cards);
}
```


# ♾️核心类
## 💫Card
通过 color 和 number 创建一张 card
```csharp
public class Card(EColor color, ENumber number) {  
    public readonly Color Color = new((int)color);  
    public readonly Number Number = new((int)number);  
}
```

> 注意, EColor 和 ENumber 为 枚举类型, 用来约束用户


## 💫Deck

- 获得一个包含52张牌的牌盒
- Sort: 排序
- Shuffle: 随机打乱
- Draw: 从牌堆中抽牌

```csharp
/// <summary>
///     The deck of cards, which contains 52 cards. <br/>
///     Including sort, shuffle, draw, and print functions. <br/>
/// </summary>
public class Deck {
    private readonly List<Card> _deck;
    public int Count => _deck.Count;

    /// <summary>
    ///     Create a new deck of 52 cards. <br/>
    ///     Excluding 2 joker cards.
    /// </summary>
    public Deck() {}

    /// <summary>
    ///     Sort the deck by color and number. <br/>
    ///     Sort by the color code and number value, so A is the biggest card.
    /// </summary>
    public void Sort() {}

    /// <summary>
    ///     Shuffle the deck.
    /// </summary>
    public void Shuffle() {}

    /// <summary>
    ///     Draw n cards from the deck. <br/>
    /// </summary>
    /// <param name="n">The number of drawn cards</param>
    /// <returns>
    ///     Drawn cards. <br/>
    ///     If there's no enough cards in the deck, it won't throw exception, but return a blank list
    /// </returns>
    public List<Card> Draw(int n) {}
}
```


## 💫Hand

- 传入包含 k 张 card 的 List(k >= 5), 返回德州扑克规则中最大的五张牌的组合
- 注意: 使用时通常用第一个构造函数, 另一个是用来方便测试的!

```csharp
public class Hand {
    private readonly HandHandler _handHandler = HandHandler.Instance;

    // Initial cards
    public readonly List<Card> Cards;

    // Max combination 5 cards for the hand
    public readonly List<Card> MaxCombination;

    // When two hand are the same type, use ranks to compare
    public readonly List<int> Ranks;

    // Hand type, e.g., Straight, Flush, etc.
    public readonly EHandType Type;

    public Hand(List<Card> cards) {}

    /// <summary>
    ///     Warning: This constructor is used for testing
    /// </summary>
    public Hand(EHandType type, List<int> ranks) {}
}
```

# ♾️核心处理器
## 💫ICardHandler

给card进行排序

```csharp
public interface ICardHandler {
    /// <summary>
    ///   Sorts the cards by their number in descending order.
    /// </summary>
    /// <param name="cards">The cards that need to be sorted</param>
    void SortCardsByNumber(List<Card> cards);

    /// <summary>
    ///    Sorts the cards by their color in ascending order. <br/>
    ///    If two cards have the same color, they are sorted by their number in descending order.
    /// </summary>
    /// <param name="cards">The cards that need to be sorted</param>
    void SortCardsByColor(List<Card> cards);
}
```

## 💫ITypeHandler

判断牌型

```csharp
/// <summary>
///     Interface for handling poker hands, which is used to judge the type of hand. <br/>
/// </summary>
public interface ITypeHandler {
    /// <summary>
    ///   Check if the hand is a Flush. <br/>
    /// </summary>
    /// <param name="cards">The cards that need to be judged</param>
    /// <param name="ranks">Out ranks param</param>
    /// <returns>Bool result</returns>
    bool IsFlush(List<Card> cards, out List<int> ranks);

    /// <summary>
    ///   Check if the hand is a Straight. <br/>
    /// </summary>
    /// <param name="cards">The cards that need to be judged</param>
    /// <param name="ranks">Out ranks param</param>
    /// <returns>Bool result</returns>
    bool IsStraight(List<Card> cards, out List<int> ranks);

    /// <summary>
    ///   Check if the hand is Four of a Kind. <br/>
    /// </summary>
    /// <param name="cards">The cards that need to be judged</param>
    /// <param name="ranks">Out ranks param</param>
    /// <returns>Bool result</returns>
    bool IsFourOfAKind(List<Card> cards, out List<int> ranks);

    /// <summary>
    ///   Check if the hand is a Full House. <br/>
    /// </summary>
    /// <param name="cards">The cards that need to be judged</param>
    /// <param name="ranks">Out ranks param</param>
    /// <returns>Bool result</returns>
    bool IsFullHouse(List<Card> cards, out List<int> ranks);

    /// <summary>
    ///   Check if the hand is Three of a Kind. <br/>
    /// </summary>
    /// <param name="cards">The cards that need to be judged</param>
    /// <param name="ranks">Out ranks param</param>
    /// <returns>Bool result</returns>
    bool IsThreeOfAKind(List<Card> cards, out List<int> ranks);

    /// <summary>
    ///   Check if the hand is Two Pair. <br/>
    /// </summary>
    /// <param name="cards">The cards that need to be judged</param>
    /// <param name="ranks">Out ranks param</param>
    /// <returns>Bool result</returns>
    bool IsTwoPair(List<Card> cards, out List<int> ranks);

    /// <summary>
    ///   Check if the hand is One Pair. <br/>
    /// </summary>
    /// <param name="cards">The cards that need to be judged</param>
    /// <param name="ranks">Out ranks param</param>
    /// <returns>Bool result</returns>
    bool IsOnePair(List<Card> cards, out List<int> ranks);
}
```

## 💫IHandHandler

- 手牌处理器
	- CompareTwoHands: 比较两副手牌大小
	- GetMaxHands: 从多副手牌中获得最大的手牌组
- 下面两个方法主要是 Hand 类内部使用的
	- GetCombinations: 从 k 张 card 中, 取出 n 张进行组合
	- GetTypeAndRanks: 根据手牌判断其 tpye 和 ranks

```csharp
public interface IHandHandler {
    /// <summary>
    ///   According to the given type and ranks, compare two hands. <br/>
    /// </summary>
    /// <returns>
    ///   1: hand1 > hand2 <br/>
    ///   0: hand1 = hand2 <br/>
    ///   -1: hand1 less than  hand2  <br/>
    /// </returns>
    int CompareTwoHands(EHandType type1, EHandType type2, List<int> ranks1, List<int> ranks2);

    /// <summary>
    ///   Compare two hands. <br/>
    /// </summary>
    /// <returns>
    ///   1: hand1 > hand2 <br/>
    ///   0: hand1 = hand2 <br/>
    ///   -1: hand1 less than  hand2  <br/>
    /// </returns>
    int CompareTwoHands(Hand hand1, Hand hand2);

    /// <summary>
    ///   Get the max hands' index from the given hands. <br/>
    /// </summary>
    /// <returns>
    ///    A list of max hands' index from the given hands. <br/>
    /// </returns>
    List<int> GetMaxHands(List<Hand> hands);

    /// <summary>
    ///     Get all combinations of n cards from the given list of cards.
    /// </summary>
    /// <param name="cards">Given list of cards</param>
    /// <param name="n">The number of combination cards</param>
    /// <returns>
    ///     A list of all combinations of n cards from the given list of cards.
    /// </returns>
    List<List<Card>> GetCombinations(List<Card> cards, int n);

    /// <summary>
    ///     According to the cards, determine the type of hand and the ranks. <br/>
    /// </summary>
    /// <returns>
    ///     A tuple containing the type of hand and the ranks. <br/>
    /// </returns>
    (EHandType type, List<int> ranks) GetTypeAndRanks(List<Card> cards);
}
```

