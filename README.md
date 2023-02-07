# Before Refactoring 

```swift
class CardGame {
    private let deck: Deck
    private let randomizer: Randomizer

    init(deck: Deck, randomizer: Randomizer = DefaultRandomizer()) {
        self.deck = deck
        self.randomizer = randomizer
    }

    func drawRandomCard() -> Card {
        let index = randomizer.randomNumber(in: 0..<deck.count)
        let card = deck[index]
        return card
    }
}

protocol Randomizer {
    func randomNumber(in range: Range<Int>) -> Int
}

class DefaultRandomizer: Randomizer {
    func randomNumber(in range: Range<Int>) -> Int {
        .random(in: range)
    }
}
```

# Refactoring

```swift
class CardGame {
    typealias Randomizer = (Range<Int>) -> Int

    private let deck: Deck
    private let randomizer: Randomizer

    init(deck: Deck, randomizer: @escaping Randomizer = Int.random) {
        self.deck = deck
        self.randomizer = randomizer
    }

    func drawRandomCard() -> Card {
        let index = randomizer(0..<deck.count)
        let card = deck[index]
        return card
    }
}
```

```
class CardGameTests: XCTestCase {
    func testDrawingRandomCard() {
        var randomizationRange: Range<Int>?

        let deck = Deck(cards: [Card(value: .ace, suit: .spades)])

        let game = CardGame(deck: deck, randomizer: { range in
            // Capture the range to be able to assert on it later
            randomizationRange = range

            // Return a constant value to remove randomness from
            // our test, making it run consistently.
            return 0
        })

        XCTAssertEqual(randomizationRange, 0..<1)
        XCTAssertEqual(game.drawRandomCard(), Card(value: .ace, suit: .spades))
    }
}
```
