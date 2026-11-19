# Good and Bad Tests

## Good Tests

**Integration-style**: Test through real interfaces, not mocks of internal parts.

```swift
// GOOD: Tests observable behavior
@Test func `user can checkout with valid cart`() async throws {
    var cart = Cart()
    cart.add(product)
    let result = try await checkout(cart, paymentMethod: paymentMethod)
    #expect(result.status == .confirmed)
}
```

Characteristics:

- Tests behavior users/callers care about
- Uses public API only
- Survives internal refactors
- Describes WHAT, not HOW
- One logical assertion per test

## Bad Tests

**Implementation-detail tests**: Coupled to internal structure.

```swift
// BAD: Tests implementation details
@Test func `checkout calls paymentService.process`() async throws {
    let paymentService = PaymentServiceSpy()
    let checkout = Checkout(paymentService: paymentService)
    _ = try await checkout.checkout(cart, paymentMethod: paymentMethod)
    #expect(paymentService.processedAmounts == [cart.total])
}
```

Red flags:

- Mocking internal collaborators
- Testing private methods
- Asserting on call counts/order
- Test breaks when refactoring without behavior change
- Test name describes HOW not WHAT
- Verifying through external means instead of interface

```swift
// BAD: Bypasses interface to verify
@Test func `createUser saves to database`() async throws {
    _ = try await createUser(name: "Alice")
    let row = try await database.findUser(named: "Alice")
    #expect(row != nil)
}

// GOOD: Verifies through interface
@Test func `createUser makes user retrievable`() async throws {
    let user = try await createUser(name: "Alice")
    let retrieved = try await getUser(id: user.id)
    #expect(retrieved.name == "Alice")
}
```

**Tautological tests**: Expected value restates the implementation, so the test passes by construction.

```swift
// BAD: Expected value is recomputed the way the code computes it
@Test func `calculateTotal sums line items`() {
    let items = [LineItem(price: 10), LineItem(price: 5)]
    let expected = items.reduce(0) { $0 + $1.price }
    #expect(calculateTotal(items) == expected)
}

// GOOD: Expected value is an independent, known literal
@Test func `calculateTotal sums line items`() {
    let items = [LineItem(price: 10), LineItem(price: 5)]
    #expect(calculateTotal(items) == 15)
}
```
