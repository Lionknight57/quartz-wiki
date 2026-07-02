---
title: Testing
tags: [testing, unit-testing, e2e, tdd, bdd, mocking]
related: [CSharp, Angular, ASP-NET, Design Patterns]
sources: [Cypress.md, Jasmine.md, Mocking.md, NSubstitute.md, SpecFlow.md, Protractor.md, Unit Testing/Moq.md, Unit Testing/Testing Legacy Code.md, Unit Testing/XUnit and Test Driven and Scenario Based Testing-.md]
updated: 2026-04-12
---

# Testing

---

## Test types

| Type | What it tests | Speed | Isolation |
|---|---|---|---|
| **Unit** | Single class/function in isolation | Fast | Full (all deps mocked) |
| **Integration** | Multiple real components together | Medium | Partial |
| **E2E (End-to-End)** | Full user flow through the real UI | Slow | None |

---

## xUnit (.NET)

The preferred test framework for .NET Core. Tests run in parallel across classes, serially within a class.

```csharp
public class OrderServiceTests
{
    [Fact]
    public void CalculateTotal_ReturnsCorrectSum()
    {
        // Arrange
        var service = new OrderService();
        // Act
        var total = service.CalculateTotal(new[] { 10m, 20m });
        // Assert
        Assert.Equal(30m, total);
    }

    [Theory]
    [InlineData(10, 20, 30)]
    [InlineData(0, 5, 5)]
    [InlineData(-1, 1, 0)]
    public void CalculateTotal_ParameterisedCases(decimal a, decimal b, decimal expected)
    {
        Assert.Equal(expected, new OrderService().CalculateTotal(new[] { a, b }));
    }
}
```

**Key attributes:**

| Attribute | Purpose |
|---|---|
| `[Fact]` | A single test |
| `[Theory]` | Data-driven test (requires `[InlineData]`, `[ClassData]`, or `[MemberData]`) |
| `[InlineData(...)]` | Inline parameter set for `[Theory]` |
| `[ClassData(typeof(T))]` | Data from a class implementing `IEnumerable<object[]>` |
| `[MemberData(nameof(X))]` | Data from a static property/method |
| `[Collection("name")]` | Force tests to run serially across classes |

**Setup / teardown:** Use constructor (setup) and `IDisposable.Dispose()` (teardown) — no `[SetUp]`/`[TearDown]` like NUnit.

**Logging in tests:**
```csharp
public class MyTests
{
    private readonly ITestOutputHelper _output;
    public MyTests(ITestOutputHelper output) => _output = output;

    [Fact]
    public void Test() => _output.WriteLine("debug info here");
}
```

**NuGet packages needed:**
- `Microsoft.NET.Test.Sdk`
- `xunit`
- `xunit.runner.visualstudio`
- `Moq` or `NSubstitute`

---

## Moq (.NET mocking)

Moq creates mock implementations of interfaces or virtual methods.

```csharp
var mock = new Mock<ICustomerRepository>();

// Setup a return value
mock.Setup(r => r.GetById(42)).Returns(new Customer { Id = 42, Name = "Ken" });

// Setup with any input
mock.Setup(r => r.GetById(It.IsAny<int>())).Returns(new Customer());

// Setup with range constraint
mock.Setup(r => r.GetById(It.IsInRange(1, 100, Range.Inclusive))).Returns(customer);

// Setup sequence of returns
mock.SetupSequence(r => r.GetNext())
    .Returns(customer1)
    .Returns(customer2);

// Verify it was called
mock.Verify(r => r.GetById(42), Times.Once);
mock.Verify(r => r.Save(It.IsAny<Customer>()), Times.Never);

// Use the mock
var service = new OrderService(mock.Object);
```

---

## NSubstitute (.NET mocking)

Cleaner API alternative to Moq. Only interfaces and virtual methods can be substituted.

```csharp
private readonly ICustomerRepository _repo = Substitute.For<ICustomerRepository>();
private readonly ILoggingService _log = Substitute.For<ILoggingService>();

// Return a value
_repo.GetByIdAsync(customerId).Returns(customerDto);

// Verify call was received
_log.Received(1).LogInformation("Retrieved a customer with Id: {Id}", customerId);
_log.DidNotReceive().LogInformation("Unable to find...", Arg.Any<Guid>());
```

---

## SpecFlow — BDD (.NET)

SpecFlow implements **Behaviour-Driven Development (BDD)** using **Gherkin** — a natural-language test format that bridges business and development.

### Gherkin syntax

```gherkin
Feature: Process Payment
  As a customer
  I want to pay for my order
  So that it gets dispatched

  Scenario: Successful payment
    Given I have an order worth $50
    And my card has sufficient funds
    When I submit payment
    Then the payment should succeed
    And the order status should be "Dispatched"

  Scenario Outline: Payment with various amounts
    Given I have an order worth <amount>
    When I submit payment
    Then the result should be <result>

    Examples:
      | amount | result  |
      | $10    | success |
      | $0     | failure |
```

**Keywords:**
- `Feature` — groups related scenarios
- `Scenario` — one test case
- `Scenario Outline` + `Examples` — parameterised scenarios
- `Given` — precondition/setup
- `When` — action
- `Then` — assertion
- `And` / `But` — continue same step type
- `Background` — shared `Given` steps for all scenarios in a feature

**Step definitions** (.NET):
```csharp
[Binding]
public class PaymentSteps
{
    [Given(@"I have an order worth \$(\d+)")]
    public void GivenOrderWorth(int amount) => _order = new Order(amount);

    [When(@"I submit payment")]
    public void WhenSubmitPayment() => _result = _paymentService.Pay(_order);

    [Then(@"the payment should succeed")]
    public void ThenPaymentSucceeds() => Assert.True(_result.Success);
}
```

---

## Cypress — E2E testing (JavaScript/TypeScript)

End-to-end testing framework that runs in the browser. Tests real user flows.

```bash
npx cypress open    # open interactive test runner
npx cypress run     # headless CI run
```

**Cypress selects elements like jQuery:**
```javascript
cy.get('.submit-button').click();
cy.get('#email').type('user@example.com');
cy.get('[data-cy="username"]').should('have.value', 'Ken');
```

**Full test example:**
```javascript
describe('Login flow', () => {
  it('should log in successfully', () => {
    cy.visit('/login');
    cy.get('#email').type('ken@example.com');
    cy.get('#password').type('secret');
    cy.get('[data-cy="login-btn"]').click();
    cy.url().should('include', '/dashboard');
    cy.contains('Welcome, Ken').should('be.visible');
  });
});
```

**Key commands:**

| Command | Purpose |
|---|---|
| `cy.visit(url)` | Navigate to URL |
| `cy.get(selector)` | Select element |
| `cy.contains(text)` | Find element by text |
| `cy.click()` | Click element |
| `cy.type(text)` | Type into input |
| `cy.should(assertion)` | Assert |
| `cy.request(method, url)` | Make HTTP request |
| `cy.intercept()` | Stub/spy network requests |
| `cy.fixture(file)` | Load test data |

**Best practice:** Use `data-cy` attributes as selectors — they won't change when CSS or structure changes.

---

## Testing AAA pattern

All good tests follow: **Arrange → Act → Assert**

```csharp
[Fact]
public void ProcessOrder_SendsConfirmationEmail()
{
    // Arrange
    var mockEmail = new Mock<IEmailService>();
    var service = new OrderService(mockEmail.Object);
    var order = new Order { Id = 1, CustomerEmail = "ken@example.com" };

    // Act
    service.Process(order);

    // Assert
    mockEmail.Verify(e => e.Send("ken@example.com", It.IsAny<string>()), Times.Once);
}
```

**One act per test** — if you're calling multiple methods, split into multiple tests.

---

## See also
- [[CSharp]] — xUnit, Moq, NSubstitute all use C#
- [[Angular]] — Jasmine + Karma for Angular unit tests; Cypress for E2E
- [[ASP-NET]] — integration testing with `WebApplicationFactory`
- [[Design-Patterns]] — mocks implement the same interfaces as production code
