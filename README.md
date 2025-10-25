## EX 11 : IMPLEMENTATION OF ELLIPTIC CURVE CRYPTOGRAPHY (ECC)

## AIM:

To implement the Elliptic Curve Cryptography (ECC) algorithm for basic point addition and scalar multiplication on an elliptic curve over a finite field.


## ALGORITHM:
1.	Start the program.
2.	Select the parameters of the elliptic curve:
•	Choose a prime number p (field size).
•	Choose curve parameters a and b for the equation:
y2 = x3 + ax + b modp
2.	Define a base point (generator) G(x, y) on the curve.
3.	Choose private keys:
•	User A chooses a random private key a.
•	User B chooses a random private key b.
4.	Compute public keys:
•	User A computes public key A = a * G (scalar multiplication).
•	User B computes public key B = b * G.
5.	Exchange public keys.
6.	Each user computes the shared secret:
•	User A computes: S = a * B
•	User B computes: S = b * A
•	Since ECC is based on scalar multiplication, both S values are equal.
7.	The shared secret is now established.
8.	End the program.

## PROGRAM:
```
#include <stdio.h>
#include <stdlib.h>

// Define a structure for a point on the elliptic curve
typedef struct {
    int x;
    int y;
    int is_infinity; // 1 if this point is the "point at infinity"
} Point;

// Elliptic curve parameters for y^2 = x^3 + ax + b over F_p
const int a = 2;  // Coefficient a
const int b = 3;  // Coefficient b
const int p = 17; // Prime number for the finite field F_p

// Function to perform modular operation (handles negative results)
int mod(int value, int m) {
    int result = value % m;
    return (result < 0) ? result + m : result;
}

// Function to calculate modular inverse of a number (a^-1 mod p)
int mod_inverse(int k, int p) {
    k = mod(k, p);
    for (int x = 1; x < p; x++) {
        if ((k * x) % p == 1)
            return x;
    }
    return -1; // No inverse exists
}

// Function to add two points on the elliptic curve
Point point_addition(Point P, Point Q) {
    Point result;
    result.is_infinity = 0;

    // Handle special cases
    if (P.is_infinity) return Q;
    if (Q.is_infinity) return P;

    // If x1 == x2 and y1 != y2, the result is the point at infinity
    if (P.x == Q.x && P.y != Q.y) {
        result.is_infinity = 1;
        return result;
    }

    int lambda;

    // Point doubling
    if (P.x == Q.x && P.y == Q.y) {
        int denominator = mod_inverse(2 * P.y, p);
        if (denominator == -1) {
            result.is_infinity = 1;
            return result;
        }
        lambda = mod((3 * P.x * P.x + a) * denominator, p);
    }
    // Point addition
    else {
        int denominator = mod_inverse(Q.x - P.x, p);
        if (denominator == -1) {
            result.is_infinity = 1;
            return result;
        }
        lambda = mod((Q.y - P.y) * denominator, p);
    }

    result.x = mod(lambda * lambda - P.x - Q.x, p);
    result.y = mod(lambda * (P.x - result.x) - P.y, p);

    return result;
}

// Function to perform scalar multiplication (n * P)
Point scalar_multiplication(Point P, int n) {
    Point result;
    result.is_infinity = 1; // Start with point at infinity

    Point addend = P;

    while (n > 0) {
        if (n & 1)
            result = point_addition(result, addend);

        addend = point_addition(addend, addend);
        n >>= 1;
    }
    return result;
}

// Main function to demonstrate ECC operations
int main() {
    Point G = {5, 1, 0}; // Base point on the curve
    int n = 7;           // Scalar to multiply with the point

    printf("Base point G: (%d, %d)\n", G.x, G.y);
    Point R = scalar_multiplication(G, n);

    if (R.is_infinity) {
        printf("Result of %d * G: Point at Infinity\n", n);
    } else {
        printf("Result of %d * G: (%d, %d)\n", n, R.x, R.y);
    }

    return 0;
}

```
## OUTPUT:
<img width="1260" height="663" alt="image" src="https://github.com/user-attachments/assets/89a7e336-fe68-41f4-950f-8d8414fb3201" />

## RESULT:

The ECC program successfully demonstrates point addition and scalar multiplication on an elliptic curve over a finite field, and the simulation has been executed and verified successfully.
