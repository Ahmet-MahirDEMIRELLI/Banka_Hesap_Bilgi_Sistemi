#include <conio.h>
#include <ctype.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

#define USER_INFO_FILENAME "Users.csv"
#define ACCOUNT_REQUEST_FILENAME "Requests.csv"
#define MONEY_TRANSACTIONS_FILENAME "Transactions.csv"
#define currentYear 2023
#define ADMIN_ID "admin"
#define ADMIN_PASSWORD "111111"

#define REQUEST_ACCEPTED "+"
#define REQUEST_REJECTED "-"
#define REQUEST_UNANSWERED "*"
#define MAX_NAME_LEN 30
#define MAX_ID_LEN 12
#define PASSWORD_LEN 7
#define MAX_ACCOUNT_NO_SIZE 5


#define PROMPT_WIDTH 64
#define MESSAGE_WIDTH 125
#define moveUp(x) printf("\033[%dA", (x))
#define moveRight(x) printf("\033[%dC", (x))
#define goToXY(x, y) printf("\033[%d;%dH", (y), (x))
#define clrLine() printf("\33[2K\r")
#define clrScreen() system("cls")
#define backspace() printf("\b \b")

#define getNewNode(X) (X *) malloc(sizeof(X))

typedef struct NODE_USERS {
	char name[MAX_NAME_LEN], surname[MAX_NAME_LEN];
	char ID[MAX_ID_LEN];
	char password[PASSWORD_LEN];
	char accountNumber[MAX_ACCOUNT_NO_SIZE];
	int balance;
	int birthYear;
	struct NODE_USERS *next;
} USERS;

typedef struct NODE_REQUESTS {
	char requestStatus[2];
	char name[MAX_NAME_LEN], surname[MAX_NAME_LEN];
	char ID[MAX_ID_LEN];
	char password[PASSWORD_LEN];
	char accountNumber[MAX_ACCOUNT_NO_SIZE];
	int birthYear;
	struct NODE_REQUESTS *next;
} REQUESTS;

typedef struct TRANSACTIONS_ {
	char ID[MAX_ID_LEN];
	int transferredAmount;
	char date[30];
	struct TRANSACTIONS_ *next;
} TRANSACTIONS;


//linkedList islemleri
USERS *searchUsersLL(char ID[], USERS *root);
void addUserLL(USERS **root, USERS *newNode);//returns the new root
int removeUserLL(USERS **pRoot, USERS *tar);

// File Fun
USERS *readUsers();
REQUESTS *readRequests();
void writeUsersFile(USERS *root);

//Hesap Fun
void printUserInfo(USERS *tar);
int removeUser(USERS **pRoot, USERS *tar);
void deposit(USERS *Bank, USERS *source);
void withdraw(USERS *Bank, USERS *source);
void changePass(char *);
REQUESTS *getNewAccountInfo(USERS *users, REQUESTS *requests);

void mainScreen();
void init();

int main() {
	init();
	mainScreen();

	return 0;
}

int isAdmin(USERS *node);
void getLine(char dst[], int size, char *printString);
void centerMessage(char text[]);
void anyKey();
void clrLines(int x);
void getPassword(char dst[], char *printString);
void centerPrompt(const char *text);
void getInt(int *dst, int nDigits, char *msg);
void printSelection(char **selectionMsg, int size);

void checkAllocation(void *ptr, char *str);

int getNewPassword(char *oldPassword) {
	char passwordAttempt1[PASSWORD_LEN], passwordAttempt2[PASSWORD_LEN];
	centerPrompt("Yeni sifre giriniz: ");
	centerPrompt("\nYeni sifreti tekrar giriniz:\n");
	moveUp(2);
	moveRight(PROMPT_WIDTH);
	getPassword(passwordAttempt1, NULL);
	moveRight(PROMPT_WIDTH);
	getPassword(passwordAttempt2, NULL);
	if (strcmp(passwordAttempt1, passwordAttempt2) != 0)
		return 0;
	strcpy(oldPassword, passwordAttempt1);
	return 1;
}
void changePass(char *oldPass) {
	int status;
	do {
		status = getNewPassword(oldPass);
		if (!status) {
			centerMessage("\nGirilen sifreler eslesmiyor. Yeniden deneyiniz.");
			anyKey();
			clrLines(4);
		}
	} while (!status);
}

void printUserInfo(USERS *tar) {
	centerMessage("\n-- Hesap Bilgileri --\n");
	printf("\n%*s%s", PROMPT_WIDTH, "Isim: ", tar->name);
	printf("\n%*s%s", PROMPT_WIDTH, "Soyisim: ", tar->surname);
	printf("\n%*s%s", PROMPT_WIDTH, "ID: ", tar->ID);
	printf("\n%*s%s", PROMPT_WIDTH, "Hesap Numarasi: ", tar->accountNumber);
	printf("\n%*s%d", PROMPT_WIDTH, "Bakiye: ", tar->balance);
	anyKey();
}

int removeUser(USERS **pRoot, USERS *tar) {
	if (tar->balance != 0) {
		centerMessage("\nHesabinizda para mevcuttur.");
		centerMessage("\nPara cekerek bakiyeyi sifirladiktan sonra hesabinizi sile bilirsiniz.\n");
		anyKey();
		return 0;
	}

	return removeUserLL(pRoot, tar);
}

REQUESTS *strToRequestsNode(char *str) {
	REQUESTS *node = getNewNode(REQUESTS);
	checkAllocation(node, "strToRequestsNode()");

	sscanf(str, "%[^,],%[^,],%[^,],%[^,],%[^,],%[^,],%d", node->requestStatus,
	       node->name,
	       node->surname,
	       node->ID,
	       node->accountNumber,
	       node->password,
	       &(node->birthYear));
	node->next = NULL;
	return node;
}

void addRequestsLL(REQUESTS **pRoot, REQUESTS *newNode) {
	while (*pRoot != NULL) {
		pRoot = &(*pRoot)->next;
	}
	*pRoot = newNode;
}

REQUESTS *searchRequestsLL(char *ID, REQUESTS *root) {
	while (root != NULL && strcmp(ID, root->ID) != 0)
		root = root->next;
	return root;
}
REQUESTS *readRequests() {
	FILE *fp = fopen(ACCOUNT_REQUEST_FILENAME, "r");//OPEN FILE IN READ MODE
	int bufferLen = 100;
	char *buffer = NULL;
	REQUESTS *root = NULL, *newNode;

	if (fp == NULL) {// IF FILE DOES NOT EXIST
		fp = fopen(ACCOUNT_REQUEST_FILENAME, "w");
		fclose(fp);
		return NULL;
	}

	buffer = (char *) malloc(sizeof(char) * bufferLen);
	if (buffer == NULL) {
		printf("\n\nBEKLENMEDIK BIR HATA OLUSTU! - readRequests()\n\n");
		fclose(fp);
		exit(-1);
	}

	do {
		if (fgets(buffer, bufferLen - 1, fp) != NULL) {
			newNode = strToRequestsNode(buffer);
			if (searchRequestsLL(newNode->ID, root) == NULL) {//check for duplicate accounts
				addRequestsLL(&root, newNode);
			}
		}
	} while (!feof(fp));

	free(buffer);
	fclose(fp);
	return root;
}
char *requestStatusToText(char *requestStatus) {
	if (strcmp(requestStatus, REQUEST_UNANSWERED) == 0) {
		return "talep daha cevaplandirilmadi";
	} else if (strcmp(requestStatus, REQUEST_ACCEPTED) == 0) {
		return "talep onaylandi";
	} else if (strcmp(requestStatus, REQUEST_REJECTED) == 0) {
		return "talep reddedildi";
	}
	return "HATA - requestStatusToText()";
}
int isValidID(char *ID);
int isValidName(char *name) {
	int i;
	char space[] = " ";
	char *tok = strtok(name, space);

	if (tok == NULL) return 0;
	do {
		if (!isalpha(*tok) || islower(*tok)) return 0;
		i = 1;
		while (tok[i] != '\0') {
			if (!islower(tok[i]))
				return 0;

			i++;
		}
		tok = strtok(NULL, space);
	} while (tok != NULL);
	return 1;
}
REQUESTS *getNewAccountInfo(USERS *users, REQUESTS *requests) {
	REQUESTS *newRequest, *request;
	char tmpString[30];
	int tmpInt, isValid;
	do {
		getLine(tmpString, MAX_ID_LEN, "\nID giriniz: ");
		isValid = isValidID(tmpString);
		if (!isValid) {
			centerMessage("\nID'ler 11 karakter uzunlugunda olmali ve sadece sayilardan olusmalidir.");
			anyKey();
			clrLines(4);
		}
	} while (!isValid);

	if (searchUsersLL(tmpString, users) != NULL) {
		centerMessage("\nBu ID'ye ait hesap artik olusturulmustur.");
		return NULL;
	}
	if ((request = searchRequestsLL(tmpString, requests)) != NULL) {
		centerMessage("\nBu ID'ye ait hesap acma talebi artik olusturulmustur.");
		printf("\n%*s%s", PROMPT_WIDTH - 5, "Talebin Durumu: ", requestStatusToText(request->requestStatus));
		return NULL;
	}

	do {
		getInt(&tmpInt, 4, "Dogum yilinizi giriniz: ");

		if (tmpInt < 1900 || tmpInt > currentYear) {
			centerMessage("\nLutfen gecerli bir deger giriniz.\n");
			anyKey();
			clrLines(4);
		} else if (currentYear - tmpInt < 18) {
			centerMessage("\nMalesef 18 yasindan kucuklar hesap olusturamaz.");
			return NULL;
		}
	} while (tmpInt < 1900 || tmpInt > currentYear);

	newRequest = getNewNode(REQUESTS);
	checkAllocation(newRequest, "getNewAccountInfo()");
	strcpy(newRequest->requestStatus, REQUEST_UNANSWERED);
	newRequest->birthYear = tmpInt;
	strcpy(newRequest->ID, tmpString);
	changePass(newRequest->password);
	do {
		getLine(newRequest->name, MAX_NAME_LEN, "Lutfen adinizi giriniz: ");
		isValid = isValidName(newRequest->name);
		if (!isValid) {
			centerMessage("\n\nGirilen isim gecersizdir.");
			centerMessage("\nIsimlerde bas harfin disinda diger harfler kucuk olmalidir.");
			anyKey();
			clrLines(5);
		}
	} while (!isValid);
	do {
		getLine(newRequest->surname, MAX_NAME_LEN, "Lutfen soyadinizi giriniz: ");
		isValid = isValidName(newRequest->surname);
		if (!isValid) {
			centerMessage("\n\nGirilen isim gecersizdir.");
			centerMessage("\nIsimlerde bas harfin disinda diger harfler kucuk olmalidir.");
			anyKey();
			clrLines(5);
		}
	} while (!isValid);

	tmpInt = 0;
	while (users != NULL) {
		if (tmpInt < atoi(users->accountNumber))
			tmpInt = atoi(users->accountNumber);
		users = users->next;
	}
	while (requests != NULL) {
		if (tmpInt < atoi(requests->accountNumber))
			tmpInt = atoi(requests->accountNumber);
		requests = requests->next;
	}
	sprintf(newRequest->accountNumber, "%04d", tmpInt + 1);
	newRequest->next = NULL;

	return newRequest;
}
int isValidID(char *ID) {
	if (strlen(ID) != MAX_ID_LEN - 1) {
		return 0;
	}
	while (*ID) {
		if (isdigit(*ID++) == 0) return 0;
	}
	return 1;
}
TRANSACTIONS *readTransactions();
void addTransactionLL(TRANSACTIONS **pTransactions, char *ID, int amount);
void writeTransactions(TRANSACTIONS *root);
void deposit(USERS *Bank, USERS *source) {
	int val;
	TRANSACTIONS *transactions = readTransactions();
	centerMessage("\n-- Para Yatirma --\n");
	getInt(&val, 9, "\nHesabiniza yatiracaginiz tutari giriniz: ");
	if (val <= 0) {
		centerMessage("\nGecersiz deger girildi. Islem basarisiz.\n");
		anyKey();
		return;
	}
	addTransactionLL(&transactions, source->ID, val);
	writeTransactions(transactions);
	source->balance += val;
	Bank->balance += val;
	printf("\n%*s%dTL hesabiniza yatirildi.\n", PROMPT_WIDTH - 10, "Islem basarili. ", val);
	anyKey();
}

void writeTransactions(TRANSACTIONS *root) {
	FILE *fp = fopen(MONEY_TRANSACTIONS_FILENAME, "w+");//CREATE FILE
	checkAllocation(fp, "writeTransactions()");

	while (root != NULL) {
		fprintf(fp, "%s,%d,%s\n", root->ID, root->transferredAmount, root->date);
		root = root->next;
	}
	fclose(fp);
}
void setCurrentDate(char *date);
void addTransactionLL(TRANSACTIONS **pTransactions, char *ID, int amount) {
	TRANSACTIONS *newNode = getNewNode(TRANSACTIONS);
	checkAllocation(newNode, "addTransactionLL()");
	strcpy(newNode->ID, ID);
	newNode->transferredAmount = amount;
	newNode->next = *pTransactions;
	setCurrentDate(newNode->date);
	*pTransactions = newNode;
}
void setCurrentDate(char *date) {
	time_t t = time(NULL);
	struct tm tm = *localtime(&t);
	sprintf(date, "%02d.%02d.%d", tm.tm_mday, tm.tm_mon + 1, tm.tm_year + 1900);
}

TRANSACTIONS *strToTransactionNode(char *str);
TRANSACTIONS *readTransactions() {
	FILE *fp = fopen(MONEY_TRANSACTIONS_FILENAME, "r");//OPEN FILE IN READ MODE
	int bufferLen = 100;
	char *buffer = NULL;
	TRANSACTIONS *root = NULL, **pTail = NULL, *newNode;

	if (fp == NULL) {// IF FILE DOES NOT EXIST
		return NULL;
	}

	buffer = (char *) malloc(sizeof(char) * bufferLen);
	if (buffer == NULL) {
		printf("\n\nBEKLENMEDIK BIR HATA OLUSTU! - readRequests()\n\n");
		fclose(fp);
		exit(-1);
	}

	pTail = &root;
	do {
		if (fgets(buffer, bufferLen - 1, fp) != NULL) {
			newNode = strToTransactionNode(buffer);
			(*pTail) = newNode;
			pTail = &newNode->next;
		}
	} while (!feof(fp));

	free(buffer);
	fclose(fp);
	return root;
}
TRANSACTIONS *strToTransactionNode(char *str) {
	TRANSACTIONS *node = getNewNode(TRANSACTIONS);
	checkAllocation(node, "strToTransactionNode()");

	sscanf(str, "%[^,],%d,%[^\n]\n",
	       node->ID,
	       &node->transferredAmount,
	       node->date);
	node->next = NULL;
	return node;
}

void withdraw(USERS *Bank, USERS *source) {
	int transferedAmount;
	TRANSACTIONS *transactions = readTransactions();
	centerMessage("\n-- Para Cekme --\n");
	getInt(&transferedAmount, 9, "\nHesabinizdan cekeceginiz tutari giriniz: ");
	if (transferedAmount > source->balance) {
		centerMessage("\nHesabinizda yeterli bakiye yoktur. Islem basarisiz. \n");
		anyKey();
		return;
	}
	addTransactionLL(&transactions, source->ID, -transferedAmount);
	writeTransactions(transactions);
	source->balance -= transferedAmount;
	Bank->balance -= transferedAmount;
	printf("\n%*s%dTL hesabinizdan cekildi.\n", PROMPT_WIDTH - 10, "Islem basarili. ", transferedAmount);
	anyKey();
}

USERS *searchUsersLL(char ID[30], USERS *root) {
	while (root != NULL && strcmp(ID, root->ID) != 0)
		root = root->next;
	return root;
}

void addUserLL(USERS **pRoot, USERS *newNode) {
	while (*pRoot != NULL) {
		pRoot = &(*pRoot)->next;
	}
	*pRoot = newNode;
}


int removeUserLL(USERS **pRoot, USERS *tar) {
	USERS *tmp;
	if (strcmp(tar->ID, ADMIN_ID) == 0) {
		centerMessage("\nBanka hesabini silemezsiniz.");
		anyKey();
		return 0;
	}
	while ((*pRoot) != NULL && (*pRoot) != tar)
		pRoot = &(*pRoot)->next;

	if (*pRoot == tar) {
		tmp = (*pRoot);
		*pRoot = (*pRoot)->next;
		free(tmp);
		centerMessage("\nBanka hesabi basariyla silindi.");
		anyKey();
		return 1;
	} else {
		centerMessage("\nBeklenmedik hata olustu. Hesap silinemedi.");
		anyKey();
		return 0;
	}
}

USERS *adminNode() {
	USERS *admin = (USERS *) calloc(1, sizeof(USERS));
	checkAllocation(admin, "adminNode()");
	printf("Users yoktu yeni Bank hesabi kuruldu %s", USER_INFO_FILENAME);
	strcpy(admin->name, "Bank");
	strcpy(admin->surname, "####");
	strcpy(admin->ID, ADMIN_ID);
	admin->birthYear = 0;
	strcpy(admin->password, ADMIN_PASSWORD);
	strcpy(admin->accountNumber, "0000");
	admin->balance = 10000;
	return admin;
}

USERS *strToUsersNode(char *str) {
	USERS *node = getNewNode(USERS);
	checkAllocation(node, "strToUsersNode()");
	sscanf(str, "%[^,],%[^,],%[^,],%d,%[^,],%[^,],%d",
	       node->name,
	       node->surname,
	       node->ID,
	       &(node->birthYear),
	       node->password,
	       node->accountNumber,
	       &(node->balance));
	node->next = NULL;
	return node;
}
USERS *readUsers() {
	FILE *fp = fopen(USER_INFO_FILENAME, "r");//OPEN FILE IN READ MODE
	int bufferLen = 100, noAdmin = 0;
	char *buffer = NULL;
	USERS *root = NULL, *newNode;

	if (fp == NULL) {// IF FILE DOES NOT EXIST
		fclose(fp);
		root = adminNode();
		writeUsersFile(root);// CRATE USERS FILE WITH ADMIN INFO INSIDE
		return root;
	}

	buffer = (char *) malloc(sizeof(char) * bufferLen);
	if (buffer == NULL) {
		printf("\n\nBEKLENMEDIK BIR HATA OLUSTU! - readUsers() - 2\n\n");
		fclose(fp);
		exit(-1);
	}

	do {
		if (fgets(buffer, bufferLen - 1, fp) != NULL) {
			newNode = strToUsersNode(buffer);
			if (searchUsersLL(newNode->ID, root) == NULL) {//check for duplicate accounts
				addUserLL(&root, newNode);
				if (isAdmin(newNode)) {//check for existence of admin account
					noAdmin = 1;
				}
			}
		}
	} while (!feof(fp));

	if (noAdmin == 0) {               // if ADMIN account does not exist
		addUserLL(&root, adminNode());// add admin account
	}

	free(buffer);
	fclose(fp);
	return root;
}
void writeUsersFile(USERS *root) {
	FILE *fp = fopen(USER_INFO_FILENAME, "w+");//CREATE FILE
	checkAllocation(fp, "writeUserToFile()");

	while (root != NULL) {
		fprintf(fp, "%s,%s,%s,%04d,%s,%s,%d\n", root->name, root->surname, root->ID, root->birthYear, root->password, root->accountNumber, root->balance);
		root = root->next;
	}
	fclose(fp);
}
void writeRequestsFile(REQUESTS *root) {
	FILE *fp = fopen(ACCOUNT_REQUEST_FILENAME, "w+");//CREATE FILE
	checkAllocation(fp, "writeRequestToFile()");

	while (root != NULL) {
		fprintf(fp, "%s,%s,%s,%s,%s,%s,%04d\n", root->requestStatus, root->name, root->surname, root->ID, root->accountNumber, root->password, root->birthYear);
		root = root->next;
	}
	fclose(fp);
}

int printMainMenu();
void sendAccountCreationRequest();
void accountOperationsScreen();

void mainScreen() {
	int choice = 0;

	do {
		printMainMenu();
		getInt(&choice, 1, "\nSeciminizi giriniz: ");
		switch (choice) {
			case 0:
				break;
			case 1:
				accountOperationsScreen();
				break;
			case 2:
				sendAccountCreationRequest();
				break;
			default:
				centerMessage("\nGecersiz deger girdiniz! Lutfen yeniden deneyiniz.");
				anyKey();
				break;
		}
		clrScreen();
	} while (choice != 0);
}

void getLoginCredidentials(char *ID, char *password) {
	centerPrompt("\nID giriniz: ");
	centerPrompt("\nSifre giriniz: ");
	goToXY(PROMPT_WIDTH + 1, 2);
	getLine(ID, MAX_ID_LEN, NULL);
	moveRight(PROMPT_WIDTH);
	getPassword(password, NULL);
}

USERS *login(USERS *root) {
	char ID[MAX_ID_LEN], password[PASSWORD_LEN];
	USERS *account;
	int tries = 0, leave = 0, maxNumberOfTries = 3;
	clrScreen();
	do {
		getLoginCredidentials(ID, password);
		tries++;
		account = searchUsersLL(ID, root);
		if (account != NULL && strcmp(account->password, password) == 0)
			leave = 1;
		else if (tries > maxNumberOfTries - 1) {
			centerMessage("\nYanlis ID veya SIFRE!");
			centerMessage("\nGiris deneme sayisini asdiniz.");
			centerMessage("\nAna menuye donuluyor.");
			anyKey();
			return NULL;
		} else {
			centerMessage("\nYanlis ID veya SIFRE!");
			anyKey();
			clrLines(5);
		}
	} while (!leave);

	centerMessage("\nBasariyla giris yapildi!");
	anyKey();

	return account;
}

void centerPrompt(const char *text) {
	int i;

	for (i = 0; text[i] == '\n'; ++i)
		putchar('\n');
	text = text + i;

	printf("%*s", PROMPT_WIDTH, text);
}
int printAccountSubmenu();
void printTransactions(USERS *account);
void printTransaction(TRANSACTIONS *transaction);
void freeUSERS(USERS *users);
void freeREQUESTS(REQUESTS *requests);
void customerMenu(USERS *hesab, USERS **pUsers) {
	USERS *Bank = searchUsersLL(ADMIN_ID, *pUsers);
	int choice, isDeleted;
	do {
		printAccountSubmenu();
		getInt(&choice, 1, "\nSeciminizi giriniz: ");
		switch (choice) {
			case 0:
				break;
			case 1:
				clrScreen();
				printUserInfo(hesab);
				break;
			case 2:
				clrScreen();
				printTransactions(hesab);
				break;
			case 3:
				clrScreen();
				deposit(Bank, hesab);
				writeUsersFile(*pUsers);
				break;
			case 4:
				clrScreen();
				withdraw(Bank, hesab);
				writeUsersFile(*pUsers);
				break;
			case 5:
				clrScreen();
				centerMessage("\n-- Parola Degistirme --\n\n");
				changePass(hesab->password);
				writeUsersFile(*pUsers);
				centerMessage("\nSifre basariyla degistirildi.");
				anyKey();
				break;
			case 6:
				clrScreen();
				isDeleted = removeUser(pUsers, hesab);
				if (isDeleted) {
					writeUsersFile(*pUsers);
					choice = 0;
				}
				break;
			default:
				printf("\nGecersiz deger girdiniz! Lutfen yeniden deneyiniz.\n\n\n");
				break;
		}
		clrScreen();
	} while (choice != 0);
}

void printTransactions(USERS *account) {
	TRANSACTIONS *transactions = readTransactions();
	int moneyIn, moneyOut, numberOfTransactions;
	moneyIn = moneyOut = numberOfTransactions = 0;
	centerMessage("\n  -- Hesap Hareketleri --\n\n");
	while (transactions != NULL) {
		if (strcmp(transactions->ID, account->ID) == 0) {
			printTransaction(transactions);
			if (transactions->transferredAmount > 0)
				moneyIn += transactions->transferredAmount;
			else
				moneyOut += transactions->transferredAmount;
			numberOfTransactions++;
		}
		transactions = transactions->next;
	}
	if (numberOfTransactions == 0) {
		centerMessage("Bu hesap para transferi gerceklestirmemistir.");
	} else {
		centerMessage("\nHesaba yatirilan toplam miktar: ");
		printf("%dTL\n", moneyIn);
		centerMessage("Hesapdan cekilen toplam miktar: ");
		printf("%dTL\n", moneyOut);
	}

	anyKey();
}
void printTransaction(TRANSACTIONS *transaction) {
	printf("%*s: %dTL %s.\n", PROMPT_WIDTH - 8, transaction->date, transaction->transferredAmount, transaction->transferredAmount < 0 ? "hesaptan cekildi" : "hesaba yatirildi");
}

int printAdminSubmenu() {
	static char *selectionMsg[] = {
	        "Bir Onceki Menuye Donmek",
	        "Hesap Talepleri Inceleme",
	};
	static const int size = sizeof(selectionMsg) / sizeof(char *);
	centerMessage("\n-- Admin Menu --\n");
	printSelection(selectionMsg, size);

	return size;
}

void printRequest(REQUESTS *request) {
	printf("%s %s, %d. yilda dogulmustur, Hesap Numarasi %s'dir.",
	       request->name,
	       request->surname,
	       request->birthYear,
	       request->accountNumber);
}

USERS *request2User(USERS *users, REQUESTS *request) {
	USERS *newUser = getNewNode(USERS);
	int max = 0;
	checkAllocation(newUser, "request2User()");
	strcpy(newUser->password, request->password);
	strcpy(newUser->ID, request->ID);
	while (users != NULL) {
		if (max < atoi(users->accountNumber))
			max = atoi(users->accountNumber);
		users = users->next;
	}
	sprintf(newUser->accountNumber, "%04d", max+1);
	strcpy(newUser->surname, request->surname);
	strcpy(newUser->name, request->name);
	newUser->birthYear = request->birthYear;
	newUser->balance = 0;
	newUser->next = NULL;
	return newUser;
}

void reviewAccountRequests() {
	REQUESTS *requests = readRequests(), *request;
	USERS *users = readUsers();
	int numberOfUnansweredRequests, choice, isValidChoice;
	numberOfUnansweredRequests = 0;
	clrScreen();
	request = requests;
	while (request != NULL) {
		if (strcmp(request->requestStatus, REQUEST_UNANSWERED) == 0) {
			printf("\n%*s%d: ", PROMPT_WIDTH - 30, "", numberOfUnansweredRequests + 1);
			printRequest(request);
			centerMessage("\n\nCevaplamamak: 0");
			centerMessage("\nOnaylamak: 1");
			centerMessage("\nReddetmek: 2\n");
			do {
				isValidChoice = 1;
				getInt(&choice, 1, "\nTalep ile ilgili kararinizi giriniz: ");
				switch (choice) {
					case 0:
						break;
					case 1:
						strcpy(request->requestStatus, REQUEST_ACCEPTED);
						addUserLL(&users, request2User(users, request));
						break;
					case 2:
						strcpy(request->requestStatus, REQUEST_REJECTED);
						break;
					default:
						centerMessage("\nGecersiz deger girdiniz! Lutfen yeniden deneyiniz.\n");
						anyKey();
						clrLines(5);
						isValidChoice = 0;
						break;
				}
			} while (!isValidChoice);
			numberOfUnansweredRequests++;
		}
		request = request->next;
	}
	writeRequestsFile(requests);
	writeUsersFile(users);
	freeUSERS(users);
	freeREQUESTS(requests);
	if (numberOfUnansweredRequests == 0) {
		centerMessage("\nIncelenecek hesap acma talebi yoktur.\n");
	} else {
		centerMessage("\nTum hesap acma talepleri incelenmistir.\n");
	}
	anyKey();
}
void freeREQUESTS(REQUESTS *requests) {
	REQUESTS *next;
	while (requests != NULL) {
		next = requests->next;
		free(requests);
		requests = next;
	}
}
void adminMenu() {
	int choice;
	do {
		printAdminSubmenu();
		getInt(&choice, 1, "\nSeciminizi giriniz: ");
		switch (choice) {
			case 0:
				break;
			case 1:
				reviewAccountRequests();
				break;
			default:
				centerMessage("\nGecersiz deger girdiniz! Lutfen yeniden deneyiniz.\n");
				anyKey();
				break;
		}
		clrScreen();
	} while (choice != 0);
}
void accountOperationsScreen() {
	USERS *hesab, *users = readUsers();

	hesab = login(users);
	clrScreen();
	if (hesab == NULL) return;

	if (isAdmin(hesab)) {
		adminMenu();
	} else {
		customerMenu(hesab, &users);
	}
	freeUSERS(users);
}
void freeUSERS(USERS *users) {
	USERS *next;
	while (users != NULL) {
		next = users->next;
		free(users);
		users = next;
	}
}

int printMainMenu() {
	static char *selectionMsg[] = {"Programi Kapat",
	                               "Hesaba Gir",
	                               "Hesap Olustur"};
	static const int size = sizeof(selectionMsg) / sizeof(char *);
	centerMessage("\n-- Ana Menu -- \n");
	printSelection(selectionMsg, size);

	return size;
}
int printAccountSubmenu() {
	static char *selectionMsg[] = {"Ana Menuye Don",
	                               "Hesap Bilgileri Goruntule",
	                               "Hesap Hareketlerini Goruntule",
	                               "Para Yatir",
	                               "Para Cek",
	                               "Sifre Degistir",
	                               "Hesabi Sil"};
	static const int size = sizeof(selectionMsg) / sizeof(char *);
	centerMessage("\n-- Hesap Islemleri -- \n");
	printSelection(selectionMsg, size);

	return size;
}
void printSelection(char **selectionMsg, int size) {
	int i;

	for (i = 0; i < size; ++i) {
		printf("%*d: %s\n", PROMPT_WIDTH - 18, i, selectionMsg[i]);
	}
}

int isAdmin(USERS *node) {
	return strcmp(node->ID, ADMIN_ID) == 0 && strcmp(node->password, ADMIN_PASSWORD) == 0;
}


void checkAllocation(void *ptr, char *str) {
	if (ptr == NULL) {
		printf("Bellek atanirken hata olustu! - %s", str);
		exit(-1);
	}
}
void init() {
	FILE *fp = fopen(ACCOUNT_REQUEST_FILENAME, "r");//OPEN REQUESTS FILE IN READ MODE

	if (fp == NULL) {// IF FILE DOES NOT EXIST
		fp = fopen(ACCOUNT_REQUEST_FILENAME, "w");
	}
	fclose(fp);
	fp = fopen(USER_INFO_FILENAME, "r");//OPEN USERS FILE IN READ MODE

	if (fp == NULL) {               // IF FILE DOES NOT EXIST
		writeUsersFile(adminNode());// CRATE USERS FILE WITH ADMIN INFO INSIDE
	}
}
void sendAccountCreationRequest() {
	REQUESTS *requests = readRequests();
	USERS *users = readUsers();
	clrScreen();
	REQUESTS *newRequest = getNewAccountInfo(users, requests);
	if (newRequest != NULL) {
		addRequestsLL(&requests, newRequest);
		writeRequestsFile(requests);
		writeUsersFile(users);
		centerMessage("\nHesap acma talebi basariyla olusturuldu.");
		centerMessage("\nTalebiniz onaylandiginda belirlediginiz bilgilerle");
		centerMessage("\nhesabiniza giris yapabileceksiniz.");
	} else {
		centerMessage("\n\nAna menuye donuluyor.");
	}
	anyKey();
	freeUSERS(users);
	freeREQUESTS(requests);
}
void getLine(char *dst, int size, char *printString) {
	int i = 0, leave = 0;
	char ch;

	if (printString != NULL)
		centerPrompt(printString);

	fflush(stdin);

	while (!leave) {
		ch = (char) getch();
		if (ch == 13) {
			if (i != 0)
				leave = 1;
		} else if (ch == '\b') {
			if (i > 0) {
				backspace();
				i--;
			}
		} else if (i < size - 1) {
			if (isprint(ch)) {
				putchar(ch);
				dst[i++] = ch;
			}
		} else
			putchar('\07');
	}
	putchar('\n');
	dst[i] = '\0';
}
void getPassword(char *dst, char *printString) {
	int i = 0, leave = 0;
	char ch;

	if (printString != NULL)
		centerPrompt(printString);

	fflush(stdin);

	while (!leave) {
		ch = (char) getch();
		if (ch == 13) {
			if (i == PASSWORD_LEN - 1)
				leave = 1;
			else
				putchar('\07');
		} else if (ch == '\b') {
			if (i > 0) {
				backspace();
				i--;
			}
		} else if (i < PASSWORD_LEN - 1) {
			if (isprint(ch)) {
				putchar('*');
				dst[i++] = ch;
			}
		} else {
			putchar('\07');
		}
	}

	putchar('\n');
	dst[i] = '\0';
}
void centerMessage(char *text) {
	int i;
	unsigned int padLen = MESSAGE_WIDTH - strlen(text);

	for (i = 0; text[i] == '\n'; ++i)
		putchar('\n');

	text = text + i;

	printf("%*s%s", (padLen + i) / 2, "", text);
}
void anyKey() {
	centerMessage("\n\nDevam etmek icin her hangi bir tusa basin...");
	fflush(stdin);
	getch();
	clrLines(3);
}
void clrLines(int x) {
	if (x < 0) return;
	clrLine();
	while (--x > 0) {
		moveUp(1);
		clrLine();
	}
}
void getInt(int *dst, int nDigits, char *msg) {
	char *inputStr = calloc(nDigits + 1, sizeof(char));
	int i;
	if (inputStr == NULL) return;
	getLine(inputStr, nDigits + 1, msg);

	for (i = 0; inputStr[i] != '\0'; i++)
		if (!isdigit(inputStr[i])) {
			*dst = INT_MIN;
			return;
		}

	*dst = atoi(inputStr);

	free(inputStr);
}
