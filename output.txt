### Python Code
from decimal import Decimal, getcontext, ROUND_HALF_UP

# Set a reasonable precision for financial calculations
getcontext().prec = 28

PRINCIPAL_PROMPT = "Enter Principal Amount (e.g., 10000.00): "
RATE_PROMPT = "Enter Annual Interest Rate (e.g., 5.25 for 5.25%): "
PERIODS_PROMPT = "Enter Number of Periods (Years): "


def read_principal_decimal(prompt: str) -> Decimal:
    """Read and validate the principal amount as a non-negative Decimal."""
    while True:
        try:
            # Keep the cursor on the same line as the prompt
            user_input = input(prompt)
            # Allow commas in input (e.g., "1,000.00") by removing them
            normalized = user_input.strip().replace(",", "")
            principal_value = Decimal(normalized)
            if principal_value < 0:
                print("Invalid input: principal must be zero or positive. Please try again.")
                continue
            return principal_value
        except Exception:
            # Any parsing error is treated as non-numeric input
            print("Invalid input: please enter a numeric principal amount (e.g., 10000.00).")


def read_rate_decimal(prompt: str) -> Decimal:
    """Read and validate the annual interest rate as a Decimal (can be negative, zero, or positive)."""
    while True:
        try:
            user_input = input(prompt)
            normalized = user_input.strip().replace(",", "")
            rate_value = Decimal(normalized)
            return rate_value
        except Exception:
            print("Invalid input: please enter a numeric annual interest rate (e.g., 5.25).")


def read_periods_int(prompt: str) -> int:
    """Read and validate the number of periods (years) as a non-negative integer."""
    while True:
        user_input = input(prompt)
        normalized = user_input.strip()
        try:
            # Disallow decimals for the number of periods
            periods_value = int(normalized)
            if periods_value < 0:
                print("Invalid input: number of periods must be zero or a positive integer. Please try again.")
                continue
            return periods_value
        except Exception:
            print("Invalid input: please enter a non-negative integer for the number of periods (years).")


def format_currency_us(amount: Decimal) -> str:
    """Format a Decimal as US currency with grouping and two decimal places (half-up rounding)."""
    # Round to exactly two decimal places using HALF_UP
    quantized = amount.quantize(Decimal("0.01"), rounding=ROUND_HALF_UP)
    # Use Python formatting with grouping; f-string works with Decimal
    return f"${quantized:,.2f}"


def compute_future_value(principal: Decimal, annual_rate_percent: Decimal, periods: int) -> Decimal:
    """Compute future value by applying annual compounding once per year for 'periods' years."""
    future_value = principal
    # Convert rate percent to a Decimal multiplier: (1 + rate/100)
    rate_fraction = annual_rate_percent / Decimal("100")
    multiplier = Decimal("1") + rate_fraction

    # Apply compounding once per year
    for _ in range(periods):
        future_value = (future_value * multiplier)
    return future_value


def main():
    # Read inputs with validation and keep the cursor on the same line as prompt
    principal_amount = read_principal_decimal(PRINCIPAL_PROMPT)
    annual_interest_rate = read_rate_decimal(RATE_PROMPT)
    number_of_periods = read_periods_int(PERIODS_PROMPT)

    # Compute future value using Decimal arithmetic
    future_value = compute_future_value(principal_amount, annual_interest_rate, number_of_periods)

    # Prepare values for display: round to two decimals (half-up) and format as US currency
    principal_display = format_currency_us(principal_amount)
    future_display = format_currency_us(future_value)

    # Print the exact required output labels and values
    print("Initial Principal: " + principal_display)
    print("Future Value:    " + future_display)


if __name__ == "__main__":
    main()
