# Calculator-project01
Simple calculator to demonstrate git,python and Vs code 
# lab1_calculator.py
"""
Lab 1 - Python Calculator
Implements basic arithmetic functions, robust input, history tracking,
operations count, memory functions, and supports using previous result (ANS).
"""

from typing import Optional
import math

# Globals required by the lab
history: list[str] = []
operations_count: int = 0
last_result: Optional[float] = None
memory: float = 0.0


def fmt_number(n: float) -> str:
    """Format number for user-friendly display: drop .0 when possible."""
    if n is None:
        return "None"
    if float(n).is_integer():
        return str(int(n))
    return str(n)


# Part 1: arithmetic functions
def add(a: float, b: float) -> float:
    return a + b


def subtract(a: float, b: float) -> float:
    return a - b


def multiply(a: float, b: float) -> float:
    return a * b


def divide(a: float, b: float) -> Optional[float]:
    if b == 0:
        print("Error: division by zero")
        return None
    return a / b


def modulus(a: float, b: float) -> Optional[float]:
    if b == 0:
        print("Error: modulus by zero")
        return None
    return a % b


# Optional advanced ops
def power(a: float, b: float) -> float:
    return a ** b


def sqrt(a: float) -> Optional[float]:
    if a < 0:
        print("Error: square root of negative number")
        return None
    return math.sqrt(a)


# Part 2: robust input
def get_number_input(prompt: str, allow_ans: bool = False) -> float:
    """
    Keep prompting until a valid float is entered.
    If allow_ans is True, user can type 'ANS' to reuse the last_result.
    """
    global last_result
    while True:
        s = input(prompt).strip()
        if allow_ans and s.upper() == "ANS":
            if last_result is None:
                print("No previous result available (ANS).")
                continue
            return float(last_result)
        try:
            return float(s)
        except ValueError:
            print("Invalid input — please enter a number.")


# Part 3: history tracking
def update_history(num1: float, op: str, num2: float, result: Optional[float]) -> None:
    """Add formatted calculation string to global history."""
    entry = f"{fmt_number(num1)} {op} {fmt_number(num2)} = {fmt_number(result) if result is not None else 'Error'}"
    history.append(entry)


def display_history() -> None:
    """Show all calculations stored in history."""
    if not history:
        print("History is empty.")
        return
    print("Calculation history:")
    for i, entry in enumerate(history, start=1):
        print(f"{i}. {entry}")


def clear_history() -> None:
    """Clear the calculation history."""
    history.clear()
    print("History cleared.")


# Memory functions: M+, M-, MR, MC
def memory_add(value: float) -> None:
    global memory
    memory += value
    print(f"Memory updated: {fmt_number(memory)}")


def memory_subtract(value: float) -> None:
    global memory
    memory -= value
    print(f"Memory updated: {fmt_number(memory)}")


def memory_recall() -> float:
    print(f"Memory recall: {fmt_number(memory)}")
    return memory


def memory_clear() -> None:
    global memory
    memory = 0.0
    print("Memory cleared.")


# Helper to perform an operation and update globals
def perform_operation(op_symbol: str, a: float, b: Optional[float] = None) -> Optional[float]:
    global operations_count, last_result

    result: Optional[float] = None
    if op_symbol == "+":
        result = add(a, b)  # type: ignore
    elif op_symbol == "-":
        result = subtract(a, b)  # type: ignore
    elif op_symbol == "*":
        result = multiply(a, b)  # type: ignore
    elif op_symbol == "/":
        result = divide(a, b)  # type: ignore
    elif op_symbol == "%":
        result = modulus(a, b)  # type: ignore
    elif op_symbol == "^":
        result = power(a, b)  # type: ignore
    elif op_symbol == "sqrt":
        result = sqrt(a)
    else:
        print("Unknown operation.")
        return None

    # If result is not None, record operation
    if result is not None:
        operations_count += 1
        last_result = result
    return result


def main_menu():
    global last_result
    print("Simple Calculator — menu")
    while True:
        print("\nOptions:")
        print(" 1) Add (+)")
        print(" 2) Subtract (-)")
        print(" 3) Multiply (*)")
        print(" 4) Divide (/)")
        print(" 5) Modulus (%)")
        print(" 6) Power (^)")
        print(" 7) Square root (sqrt)")
        print(" 8) History")
        print(" 9) Clear History")
        print("10) Memory (M+, M-, MR, MC)")
        print("11) Use previous result (type 'ANS' when prompted)")
        print("12) Quit")

        choice = input("Choose option number: ").strip()
        if choice == "12":
            print("Bye.")
            break
        elif choice in {"1", "2", "3", "4", "5", "6"}:
            op_map = {"1": "+", "2": "-", "3": "*", "4": "/", "5": "%", "6": "^"}
            op = op_map[choice]
            a = get_number_input("Enter first number (or ANS): ", allow_ans=True)
            b = get_number_input("Enter second number (or ANS): ", allow_ans=True)
            result = perform_operation(op, a, b)
            if result is not None:
                print(f"Result: {fmt_number(result)}")
            update_history(a, op, b, result)
        elif choice == "7":  # sqrt
            a = get_number_input("Enter number for square root (or ANS): ", allow_ans=True)
            result = perform_operation("sqrt", a)
            if result is not None:
                print(f"Result: {fmt_number(result)}")
            update_history(a, "sqrt", 0, result)
        elif choice == "8":
            display_history()
        elif choice == "9":
            clear_history()
        elif choice == "10":  # memory menu
            print("Memory options: (a) M+ (add last result), (b) M- (subtract last result), (c) MR (recall), (d) MC (clear)")
            m = input("Choice (a/b/c/d): ").strip().lower()
            if m == "a":
                if last_result is None:
                    print("No last result to add to memory.")
                else:
                    memory_add(float(last_result))
            elif m == "b":
                if last_result is None:
                    print("No last result to subtract from memory.")
                else:
                    memory_subtract(float(last_result))
            elif m == "c":
                memory_recall()
            elif m == "d":
                memory_clear()
            else:
                print("Invalid memory option.")
        elif choice == "11":
            print(f"Current ANS: {fmt_number(last_result) if last_result is not None else 'None'}")
        else:
            print("Invalid choice. Try again.")


if __name__ == "__main__":
    main_menu()
    
