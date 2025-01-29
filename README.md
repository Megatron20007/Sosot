#include <iostream>
#include <vector>
#include <map>
#include <string>
#include <iomanip>
#include <windows.h>
using namespace std;

// Структура для хранения информации о товаре
struct Product {
    string name;
    double price;
    int quantity;
};

// Структура для хранения данных пользователя
struct User {
    string username;
    string password;
    map<string, int> shoppingList;  // товар -> количество
};

// Глобальная база данных пользователей
map<string, User> users;

// Список товаров с ценами и количеством на складе
vector<Product> products = {
    {"Хлеб", 50.0, 20}, {"Молоко", 80.0, 15}, {"Яйца", 100.0, 30},
    {"Сахар", 60.0, 25}, {"Масло", 150.0, 10}, {"Сыр", 200.0, 8},
    {"Колбаса", 300.0, 12}, {"Картофель", 40.0, 50}, {"Морковь", 35.0, 40},
    {"Яблоки", 90.0, 30}, {"Бананы", 120.0, 20}, {"Курица", 400.0, 10},
    {"Рис", 70.0, 25}, {"Макароны", 55.0, 35}, {"Соль", 20.0, 50}
};

// Функция для регистрации нового пользователя
void registerUser() {
    string username, password;
    cout << "Введите имя пользователя: ";
    cin >> username;
    if (users.find(username) != users.end()) {
        cout << "Ошибка: пользователь с таким именем уже существует.\n";
        return;
    }
    cout << "Введите пароль: ";
    cin >> password;
    users[username] = { username, password, {} };
    cout << "Пользователь успешно зарегистрирован!\n";
}

// Функция для входа в аккаунт
User* loginUser() {
    string username, password;
    cout << "Введите имя пользователя: ";
    cin >> username;
    cout << "Введите пароль: ";
    cin >> password;

    auto it = users.find(username);
    if (it != users.end() && it->second.password == password) {
        cout << "Вход выполнен успешно!\n";
        return &it->second;
    }
    cout << "Ошибка входа: неправильное имя пользователя или пароль.\n";
    return nullptr;
}

// Функция для отображения списка товаров
void showProducts() {
    cout << left << setw(3) << "№" << setw(15) << "Товар"
        << setw(10) << "Цена" << setw(10) << "Кол-во\n";
    cout << string(40, '-') << endl;
    for (size_t i = 0; i < products.size(); i++) {
        cout << left << setw(3) << i + 1
            << setw(15) << products[i].name
            << setw(10) << products[i].price
            << setw(10) << products[i].quantity << "\n";
    }
}

// Функция для добавления товаров в список покупок
void addToShoppingList(User& user) {
    showProducts();
    int choice, qty;
    cout << "Введите номер товара для добавления в список (0 - выход): ";
    while (cin >> choice && choice != 0) {
        if (choice > 0 && choice <= (int)products.size()) {
            cout << "Введите количество: ";
            cin >> qty;
            if (qty > 0 && qty <= products[choice - 1].quantity) {
                user.shoppingList[products[choice - 1].name] += qty;
                products[choice - 1].quantity -= qty;
                cout << "Товар добавлен!\n";
            }
            else {
                cout << "Некорректное количество.\n";
            }
        }
        else {
            cout << "Некорректный ввод. Попробуйте снова.\n";
        }
        cout << "Введите номер товара (0 - выход): ";
    }
}

// Функция для отображения списка покупок и общей стоимости
void showShoppingList(const User& user) {
    if (user.shoppingList.empty()) {
        cout << "Список покупок пуст.\n";
        return;
    }

    double total = 0;
    cout << left << setw(15) << "Товар" << setw(10) << "Кол-во" << setw(10) << "Цена\n";
    cout << string(35, '-') << endl;
    for (const auto& item : user.shoppingList) {
        for (const auto& product : products) {
            if (product.name == item.first) {
                double cost = product.price * item.second;
                total += cost;
                cout << left << setw(15) << item.first
                    << setw(10) << item.second
                    << setw(10) << cost << "\n";
                break;
            }
        }
    }
    cout << string(35, '-') << endl;
    cout << "Общая стоимость: " << total << " руб.\n";
}

// Основное меню программы
void menu() {
    while (true) {
        cout << "\n1. Регистрация\n"
            << "2. Вход\n"
            << "3. Выход\n"
            << "Выберите действие: ";
        int choice;
        cin >> choice;

        switch (choice) {
        case 1:
            registerUser();
            break;
        case 2: {
            User* user = loginUser();
            if (user) {
                while (true) {
                    cout << "\n1. Добавить товары в список\n"
                        << "2. Показать список покупок\n"
                        << "3. Выйти из аккаунта\n"
                        << "Выберите действие: ";
                    cin >> choice;
                    if (choice == 1) {
                        addToShoppingList(*user);
                    }
                    else if (choice == 2) {
                        showShoppingList(*user);
                    }
                    else if (choice == 3) {
                        break;
                    }
                    else {
                        cout << "Некорректный выбор.\n";
                    }
                }
            }
            break;
        }
        case 3:
            cout << "До свидания!\n";
            return;
        default:
            cout << "Некорректный выбор.\n";
        }
    }
}

int main() {
    setlocale(LC_ALL, "Russian");
    menu();
    return 0;
}
