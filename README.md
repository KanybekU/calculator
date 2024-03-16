# calculator
#include <iostream>
#include <cmath>
#include <stack>
#include <string>
#include <sstream>

using namespace std;

// Function prototypes
double calculate(const string& expression);
double power(double base, double exponent);
double sqrt(double number);
double abs(double number);
int round(double number);
void printHistory(const stack<string>& history);

int main() {
  stack<string> history; // Stores history of calculations
  string expression;

  cout << "Welcome to the Multi-function Calculator!" << endl;

  do {
    cout << "\nEnter an expression (or 'h' for history): ";
    getline(cin, expression);

    if (expression == "h") {
      printHistory(history);
    } else {
      double result = calculate(expression);
      if (isnan(result)) {
        cout << "Invalid expression!" << endl;
      } else {
        cout << "Result: " << result << endl;
        history.push(expression + " = " + to_string(result));
      }
    }

    cout << "Continue (y/n)? ";
  } while (toupper(getchar()) == 'Y');

  cout << "Goodbye!" << endl;
  return 0;
}

// Function to evaluate a mathematical expression string
double calculate(const string& expression) {
  stringstream ss(expression);
  stack<double> operands;
  char op;

  while (ss >> op) {
    if (isdigit(op)) {
      double num;
      ss.putback(op);
      ss >> num;
      operands.push(num);
    } else {
      if (operands.size() < 2) {
        return NAN; // Not a number (invalid expression)
      }
      double b = operands.top();
      operands.pop();
      double a = operands.top();
      operands.pop();

      switch (op) {
        case '+':
          operands.push(a + b);
          break;
        case '-':
          operands.push(a - b);
          break;
        case '*':
          operands.push(a * b);
          break;
        case '/':
          if (b == 0) {
            return NAN; // Divide by zero error
          }
          operands.push(a / b);
          break;
        case '%':
          if (int(b) == 0) {
            return NAN; // Modulo by zero error
          }
          operands.push(int(a) % int(b));
          break;
        default:
          return NAN; // Invalid operator
      }
    }
  }

  if (operands.size() != 1) {
    return NAN; // Invalid expression (extra operands)
  }

  return operands.top();
}

// Additional mathematical functions
double power(double base, double exponent) {
  return pow(base, exponent);
}

double sqrt(double number) {
  if (number < 0) {
    return NAN; // Square root of negative number error
  }
  return sqrt(number);
}

double abs(double number) {
  return fabs(number);
}

int round(double number) {
  return round(number);
}

// Function to print calculation history
void printHistory(const stack<string>& history) {
  if (history.empty()) {
    cout << "No calculations in history yet." << endl;
  } else {
    cout << "Calculation History:" << endl;
    stack<string> temp = history; // Create a copy to avoid modifying original
    while (!temp.empty()) {
      cout << temp.top() << endl;
      temp.pop();
    }
  }
}
