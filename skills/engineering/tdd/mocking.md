# When to Mock

Mock at **system boundaries** only:

- External APIs (payment, email, etc.)
- Databases (sometimes - prefer test DB)
- Time/randomness
- File system (sometimes)

Don't mock:

- Your own classes/modules
- Internal collaborators
- Anything you control

## Designing for Mockability

At system boundaries, design interfaces that are easy to mock:

**1. Use dependency injection**

Pass external dependencies in rather than creating them internally:

```swift
// Easy to mock
protocol PaymentClient {
    func charge(_ amount: Decimal) async throws -> Receipt
}

func processPayment(_ order: Order, using client: any PaymentClient) async throws -> Receipt {
    try await client.charge(order.total)
}

// Hard to mock
func processPayment(_ order: Order) async throws -> Receipt {
    let client = StripeClient()
    return try await client.charge(order.total)
}
```

**2. Prefer SDK-style interfaces over generic fetchers**

Create specific functions for each external operation instead of one generic function with conditional logic:

```swift
// GOOD: Each function is independently mockable
protocol OrdersAPI {
    func getUser(id: User.ID) async throws -> User
    func getOrders(userID: User.ID) async throws -> [Order]
    func createOrder(_ draft: OrderDraft) async throws -> Order
}

// BAD: Mocking requires conditional logic inside the mock
protocol API {
    func fetch(_ request: URLRequest) async throws -> Data
}
```

The SDK approach means:
- Each mock returns one specific shape
- No conditional logic in test setup
- Easier to see which endpoints a test exercises
- Type safety per endpoint
