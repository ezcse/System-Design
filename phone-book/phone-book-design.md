# Phone Book System

This project implements a phone book system in C++ that supports the following features:
- Fetching the top 10 most dialed contacts.
- Searching contacts by first, middle, or last name.
- Handling multiple middle names.
- Displaying the most dialed contacts first when searching.

## Components and Data Structures

### 1. Contact Storage
We use a `struct` to represent a contact. This includes fields for the first name, middle names, last name, phone number, and a counter to keep track of the number of times the contact has been dialed.

### 2. Search Functionality
To search contacts by first, middle, or last name efficiently, we use an `unordered_map` (hash table) to index contacts by each name component. This allows us to handle searches efficiently.

### 3. Most Dialed Contacts
To keep track of the most dialed contacts, we use a combination of a `priority_queue` (min-heap) to maintain the top 10 most dialed contacts and a map to store the dial count of each contact.

## Contact Structure

```cpp
#include <string>
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <queue>
#include <algorithm>

struct Contact {
    std::string firstName;
    std::vector<std::string> middleNames;
    std::string lastName;
    std::string phoneNumber;
    int dialCount;

    Contact(const std::string &first, const std::vector<std::string> &middle, const std::string &last, const std::string &phone)
        : firstName(first), middleNames(middle), lastName(last), phoneNumber(phone), dialCount(0) {}
};

struct CompareContacts {
    bool operator()(const Contact &a, const Contact &b) const {
        return a.dialCount < b.dialCount;
    }
};
```

## Contact Storage and Management

```cpp
class PhoneBook {
private:
    std::unordered_map<std::string, std::unordered_set<Contact*>> nameIndex;
    std::priority_queue<Contact, std::vector<Contact>, CompareContacts> mostDialedContacts;
    const size_t maxRecentSize = 10;
    std::unordered_map<std::string, Contact> phoneNumberToContact;

public:
    void addContact(const Contact &contact) {
        phoneNumberToContact[contact.phoneNumber] = contact;

        nameIndex[contact.firstName].insert(&phoneNumberToContact[contact.phoneNumber]);
        for (const auto &middleName : contact.middleNames) {
            nameIndex[middleName].insert(&phoneNumberToContact[contact.phoneNumber]);
        }
        nameIndex[contact.lastName].insert(&phoneNumberToContact[contact.phoneNumber]);
    }

    void dialContact(const std::string &phoneNumber) {
        auto it = phoneNumberToContact.find(phoneNumber);
        if (it != phoneNumberToContact.end()) {
            Contact &contact = it->second;
            contact.dialCount++;
            mostDialedContacts.push(contact);
            if (mostDialedContacts.size() > maxRecentSize) {
                mostDialedContacts.pop();
            }
        }
    }

    std::vector<Contact> searchByName(const std::string &name) const {
        auto it = nameIndex.find(name);
        if (it != nameIndex.end()) {
            std::vector<Contact> contacts(it->second.begin(), it->second.end());
            std::sort(contacts.begin(), contacts.end(), CompareContacts());
            return contacts;
        }
        return {};
    }

    std::vector<Contact> getMostDialedContacts() const {
        std::vector<Contact> result;
        auto tempQueue = mostDialedContacts;
        while (!tempQueue.empty()) {
            result.push_back(tempQueue.top());
            tempQueue.pop();
        }
        std::reverse(result.begin(), result.end());
        return result;
    }
};
```

## Explanation

1. **Contact Storage**:
   - The `PhoneBook` class uses an `unordered_map` named `nameIndex` to index contacts by each name component (first, middle, and last names). Each key in the map points to an unordered set of pointers to contacts that match the name component.
   - Contacts are stored in a map `phoneNumberToContact` for easy retrieval by phone number.

2. **Dial Count and Most Dialed Contacts**:
   - The `Contact` struct includes a `dialCount` field to track how many times a contact has been dialed.
   - The `dialContact` method increments the dial count and updates the priority queue used to maintain the top 10 most dialed contacts.

3. **Search Functionality**:
   - The `searchByName` method searches for contacts by any name component (first, middle, or last) using the `nameIndex` hash map. The results are then sorted by the `dialCount` to ensure the most dialed contacts appear first.

4. **Priority Queue**:
   - The priority queue (`mostDialedContacts`) is used to efficiently maintain and fetch the top 10 most dialed contacts. A custom comparator is used to compare contacts by their dial count.

By utilizing these data structures, the phone book system is designed to be efficient in terms of both time and space complexity, ensuring fast lookups, insertions, and maintenance of the most dialed contacts.

## Usage

Here is an example of how to use the `PhoneBook` class:

```cpp
int main() {
    PhoneBook phoneBook;

    Contact contact1("John", {"Michael"}, "Doe", "1234");
    Contact contact2("Jane", {"Ann"}, "Doe", "5678");

    phoneBook.addContact(contact1);
    phoneBook.addContact(contact2);

    phoneBook.dialContact("1234");
    phoneBook.dialContact("1234");
    phoneBook.dialContact("5678");

    auto mostDialed = phoneBook.getMostDialedContacts();
    for (const auto &contact : mostDialed) {
        std::cout << contact.firstName << " " << contact.lastName << " - " << contact.phoneNumber << " (" << contact.dialCount << ")\n";
    }

    auto searchResults = phoneBook.searchByName("Doe");
    for (const auto &contact : searchResults) {
        std::cout << contact.firstName << " " << contact.lastName << " - " << contact.phoneNumber << " (" << contact.dialCount << ")\n";
    }

    return 0;
}
```

This example demonstrates how to add contacts, dial contacts, fetch the most dialed contacts, and search for contacts by name.
