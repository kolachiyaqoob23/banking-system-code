#include <iostream>
#include <fstream>
#include <string>
#include <algorithm>
using namespace std;

// Convert to lowercase
string toLower(const string &s) {
    string x = s;
    transform(x.begin(), x.end(), x.begin(), ::tolower);
    return x;
}

// ---------------------------------------------
// SIGNUP
// ---------------------------------------------
void signupUser() {
    string user, pass, pass2;
    cout << "\n=== SIGNUP ===\n";
    cout << "Enter username: ";
    cin >> user;
    cout << "Enter password: ";
    cin >> pass;
    cout << "Confirm password: ";
    cin >> pass2;

    if (pass != pass2) {
        cout << "Passwords do not match!\n";
        return;
    }

    ofstream f("users.txt", ios::app);
    f << user << " " << pass << endl;
    cout << "Signup successful!\n";
}

// ---------------------------------------------
// LOGIN
// ---------------------------------------------
bool loginUser() {
    string u, p, du, dp;
    cout << "\n=== LOGIN ===\n";
    cout << "Username: ";
    cin >> u;
    cout << "Password: ";
    cin >> p;

    ifstream f("users.txt");
    while (f >> du >> dp) {
        if (du == u && dp == p) {
            cout << "\nLogin Successful!\n";
            return true;
        }
    }
    cout << "\nInvalid username or password!\n";
    return false;
}

// ---------------------------------------------
// ADD PRODUCT (Case-insensitive check)
// ---------------------------------------------
void addProduct() {
    string n;
    int s, pr;

    cout << "\n=== ADD PRODUCT ===\n";
    cout << "Name: ";
    cin >> n;
    cout << "Price: ";
    cin >> pr;
    cout << "Stock: ";
    cin >> s;

    // check existing
    ifstream in("products.txt");
    string name;
    int price, stock;

    while (in >> name >> price >> stock) {
        if (toLower(name) == toLower(n)) {
            cout << "Product exists — stock updated!\n";
            stock += s;

            // rewrite file
            in.close();
            ifstream read("products.txt");
            ofstream temp("temp.txt");

            while (read >> name >> price >> stock) {
                if (toLower(name) == toLower(n))
                    temp << name << " " << pr << " " << stock << endl;
                else
                    temp << name << " " << price << " " << stock << endl;
            }

            read.close();
            temp.close();
            remove("products.txt");
            rename("temp.txt", "products.txt");
            return;
        }
    }

    // new product
    ofstream out("products.txt", ios::app);
    out << n << " " << pr << " " << s << endl;
    cout << "Product Added Successfully!\n";
}

// ---------------------------------------------
// VIEW PRODUCTS
// ---------------------------------------------
void viewProducts() {
    ifstream f("products.txt");
    string n;
    int p, s;

    cout << "\n=== PRODUCT LIST ===\n";
    cout << "NAME\tPRICE\tSTOCK\n";

    while (f >> n >> p >> s) {
        cout << n << "\t" << p << "\t" << s << endl;
    }
}

// ---------------------------------------------
// SEARCH PRODUCT (case-insensitive)
// ---------------------------------------------
void searchProduct() {
    string key;
    cout << "\nEnter product name: ";
    cin >> key;

    ifstream f("products.txt");
    string n;
    int p, s;

    while (f >> n >> p >> s) {
        if (toLower(n) == toLower(key)) {
            cout << "\nFOUND!\n";
            cout << "Name: " << n << "\nPrice: " << p << "\nStock: " << s << endl;
            return;
        }
    }
    cout << "Product Not Found!\n";
}

// ---------------------------------------------
// UPDATE PRICE
// ---------------------------------------------
void updatePrice() {
    string key;
    int newPrice;

    cout << "\nEnter product name: ";
    cin >> key;
    cout << "New Price: ";
    cin >> newPrice;

    ifstream in("products.txt");
    ofstream temp("temp.txt");
    string n;
    int p, s;
    bool found = false;

    while (in >> n >> p >> s) {
        if (toLower(n) == toLower(key)) {
            temp << n << " " << newPrice << " " << s << endl;
            found = true;
        } else {
            temp << n << " " << p << " " << s << endl;
        }
    }

    in.close();
    temp.close();
    remove("products.txt");
    rename("temp.txt", "products.txt");

    if (found) cout << "Price Updated!\n";
    else cout << "Product Not Found!\n";
}

// ---------------------------------------------
// DELETE PRODUCT
// ---------------------------------------------
void deleteProduct() {
    string key;
    cout << "\nEnter product name to delete: ";
    cin >> key;

    ifstream in("products.txt");
    ofstream temp("temp.txt");
    string n;
    int p, s;
    bool found = false;

    while (in >> n >> p >> s) {
        if (toLower(n) == toLower(key)) {
            found = true;
            continue;
        }
        temp << n << " " << p << " " << s << endl;
    }

    in.close();
    temp.close();
    remove("products.txt");
    rename("temp.txt", "products.txt");

    if (found) cout << "Deleted Successfully!\n";
    else cout << "Product Not Found!\n";
}

// ---------------------------------------------
// SELL PRODUCT
// ---------------------------------------------
void sellProduct() {
    string key;
    int qty;

    cout << "\nEnter product name to sell: ";
    cin >> key;
    cout << "Enter quantity: ";
    cin >> qty;

    ifstream in("products.txt");
    ofstream temp("temp.txt");

    string n;
    int p, s;
    bool found = false;

    while (in >> n >> p >> s) {
        if (toLower(n) == toLower(key)) {
            found = true;

            if (s < qty) {
                cout << "Not enough stock!\n";
                temp << n << " " << p << " " << s << endl;
            } else {
                s -= qty;
                cout << "\n=== BILL ===\n";
                cout << "Product: " << n << endl;
                cout << "Qty: " << qty << endl;
                cout << "Total: " << qty * p << endl;
                temp << n << " " << p << " " << s << endl;
            }
        } else {
            temp << n << " " << p << " " << s << endl;
        }
    }

    in.close();
    temp.close();
    remove("products.txt");
    rename("temp.txt", "products.txt");

    if (!found)
        cout << "Product Not Found!\n";
}

// ---------------------------------------------
// MAIN MENU
// ---------------------------------------------
int main() {
    int choice;

    cout << "=== Store Management System ===\n";
    cout << "1. Signup\n2. Login\nChoose: ";
    cin >> choice;

    if (choice == 1) signupUser();
    if (!loginUser()) return 0;

    while (true) {
        cout << "\n=== MENU ===\n";
        cout << "1. Add Product\n"
             << "2. View Products\n"
             << "3. Search Product\n"
             << "4. Update Price\n"
             << "5. Delete Product\n"
             << "6. Sell Product\n"
             << "7. Exit\n"
             << "Choose: ";
        cin >> choice;

        switch (choice) {
            case 1: addProduct(); break;
            case 2: viewProducts(); break;
            case 3: searchProduct(); break;
            case 4: updatePrice(); break;
            case 5: deleteProduct(); break;
            case 6: sellProduct(); break;
            case 7: return 0;
            default: cout << "Invalid choice!\n";
        }
    }
}
