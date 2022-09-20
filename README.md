# 1-8-15-8-
#include <iostream>
#include <cstdlib>
#include <Windows.h>
#include <ctime>
#define sz 60
#define clr system("cls")
#define syst system("pause"); system("cls")
using namespace std;
int Size;
struct Airport {
	int number;
	string airport;
	char key[sz];
	void printkey() {
		cout << "Ключ: " << this->key << endl;
		cout << "Аэропорт назначения: " << this->airport << endl;
	}
};
struct Hash {
	Airport data;
	Hash* next;
};
Hash h[256];
unsigned char Rand8[256];
void Rand();
unsigned char p = 1;
int hashFunction(char* str);
void insert(Airport* e);
void rec(Hash* e);
void Out();
Airport* search(char* e);
void delKey(char* e);
void Menu();
void main() {
	SetConsoleCP(1251);
	SetConsoleOutputCP(1251);
	cout << "Введите размер базы: "; cin >> Size;
	Rand();
	Menu();
}
void Menu() {
	int choose;
	cout << "Выберите тип операции\n";
	cout << "1. Ввод рейса\n";
	cout << "2. Вывод рейсов\n";
	cout << "3. Поиск рейсов по ключу\n";
	cout << "4. Удаление по ключу\n";
	cin >> choose;
	switch (choose) {
	case 1: {
		int amount;
		Airport* e = new Airport;
		cout << "Введите количество рейсов, необходимых для добавления\n";
		cin >> amount;
		for (int i = 0; i < amount; i++) {
			cout << "Введите номер рейса: "; cin >> e->key;
			cout << "Введите аэропорт назначения: "; cin >> e->airport;
			insert(e);
		}
		Menu();
	}
	case 2: {
		cout << "Все рейсы\n";
		Out();
		Menu();
	}
	case 3: {
		cout << "Введите ключ для поиска\n";
		char* e = new char(256);
		cin >> e;
		if (search(e) == NULL) {
			cout << "Элемент не найден";
		}
		else {
			clock_t start = clock();
			Airport* t = search(e);
			if (t)  t->printkey();
			else cout << "Нет таких\n";
			clock_t end = clock();
			double seconds = (double)(end - start) / CLOCKS_PER_SEC;
			cout << "Время поиска: " << seconds << endl;
		}
		Menu();
	};
	case 4: {
		char* e = new char(256);
		cin >> e;
		delKey(e);
		Menu();
	};
	}
}
int hashFunction(char* str) {
	while (*str) p = Rand8[p ^ (*str++)];
	return p;
}
void Rand() {
	for (int i = 0; i < 256; i++) {
		Rand8[i] = rand() % Size;
	}
}
void insert(Airport* e) {
	int index = hashFunction(e->key);
	Hash* temp = &h[index];
	if (temp->data.key == NULL) {
		temp->data = *e;
		temp->next = NULL;
	}
	else {
		while (temp->next != NULL) {
			temp = temp->next;
		}
		if (temp->next == NULL) {
			Hash* t = new Hash;
			t->data = *e;
			t->next = NULL;
			temp->next = t;
		}
	}
}
void rec(Hash* e) {
	if (e->data.key) {
		cout << "Номер рейса: " << e->data.key << endl;
		cout << "Аэропорт назначения: " << e->data.airport << endl;
		if (e->next == NULL) return;
		else rec(e->next);
	}
	else {
		cout << "Пусто\n";
	}
}
void Out() {
	for (int i = 0; i < Size; i++) {
		if (h[i].data.key) {
			cout << "Номер элемента базы: " << i << endl;
			rec(&h[i]);
		}
	}
}
Airport* search(char* e) {
	int i = hashFunction(e);
	Hash* t = &h[i];
	while (t) {
		if (!strcmp(t->data.key, e)) {
			return &t->data;
		}
		if (t->next)
			t = t->next;
		else return NULL;
	};
	return NULL;
}
void delKey(char* e) {
	int i = hashFunction(e);
	Hash* t = &h[i];
	if (!strcmp(t->data.key, e)) {
		h[i] = *t->next;
		return;
	}
	while (t) {
		if (!strcmp(t->next->data.key, e)) {
			Hash* t1 = t->next->next;
			if (t1) {
				Hash* t2 = t->next;
				t->next = t1;
				delete t2;
			}
			else {
				Hash* t2 = t->next;
				t->next = NULL;
				delete t2;
			}
			return;
		}
		if (t->next)
			t = t->next;
		else return;
	};
	return;
}
