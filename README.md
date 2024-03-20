#include <iostream>
#include <sstream>
#include <stack>
#include <string>
#include <cmath>
#include <vector>

using namespace std;

int precedence(char op) {
    if (op == '+' || op == '-')
        return 1;
    if (op == '*' || op == '/')
        return 2;
    if (op == '^')
        return 3;
    return 0;
}

double applyOp(double a, double b, char op) {
    switch (op) {
        case '+': return a + b;
        case '-': return a - b;
        case '*': return a * b;
        case '/': 
            if (b == 0) {
                throw invalid_argument("Divide by zero error");
            }
            return a / b;
        case '^': return pow(a, b);
    }
    return 0;
}

double evaluate(string tokens) {
    int i;
    stack<double> values;
    stack<char> ops;

    for (i = 0; i < tokens.length(); i++) {
        if (tokens[i] == ' ')
            continue;
        else if (tokens[i] == '(') {
            ops.push(tokens[i]);
        }
        else if (isdigit(tokens[i])) {
            double val = 0;
            while (i < tokens.length() && isdigit(tokens[i])) {
                val = (val * 10) + (tokens[i] - '0');
                i++;
            }
            values.push(val);
            i--;
        }
        else if (tokens[i] == ')') {
            while (!ops.empty() && ops.top() != '(') {
                double val2 = values.top();
                values.pop();

                double val1 = values.top();
                values.pop();

                char op = ops.top();
                ops.pop();

                values.push(applyOp(val1, val2, op));
            }
            if (!ops.empty())
                ops.pop();
        }
        else {
            while (!ops.empty() && precedence(ops.top()) >= precedence(tokens[i])) {
                double val2 = values.top();
                values.pop();

                double val1 = values.top();
                values.pop();

                char op = ops.top();
                ops.pop();

                values.push(applyOp(val1, val2, op));
            }
            ops.push(tokens[i]);
        }
    }
    while (!ops.empty()) {
        double val2 = values.top();
        values.pop();

        double val1 = values.top();
        values.pop();

        char op = ops.top();
        ops.pop();

        values.push(applyOp(val1, val2, op));
    }
    return values.top();
}

int main() {
    string expr;
    char cont = 'y';
    cout << "Welcome to the Calculator!" << endl;
    while (cont == 'y' || cont == 'Y') {
        cout << "Please enter your arithmetic expression: ";
        getline(cin, expr);
        try {
            double result = evaluate(expr);
            cout << "Result: " << result << endl;
        } catch(const invalid_argument& e) {
            cout << "Error: " << e.what() << endl;
        }
        cout << "Do you want to continue? (y/n): ";
        cin >> cont;
        cin.ignore();
    }
    cout << "Thank you for using the Calculator!" << endl;
    return 0;
}
