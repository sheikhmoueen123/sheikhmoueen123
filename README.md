#include <iostream>
#include <string>

using namespace std;

struct Contact
{
    string firstName;
    string lastName;
    string phoneNumber;
    string email;

    Contact *next;
};

class PhoneDirectory
{
private:
    Contact *head;

public:
    PhoneDirectory()
    {
        head = nullptr;
    }

    ~PhoneDirectory()
    {
        // Destructor to free memory
        Contact *current = head;
        while (current != nullptr)
        {
            Contact *temp = current;
            current = current->next;
            delete temp;
        }
    }

    void addContact(const Contact &contact)
    {
        Contact *newContact = new Contact(contact);
        newContact->next = nullptr;

        if (head == nullptr)
        {
            head = newContact;
        }
        else
        {
            Contact *current = head;
            while (current->next != nullptr)
            {
                current = current->next;
            }
            current->next = newContact;
        }
    }

    void editContact(int index, const Contact &contact)
    {
        Contact *current = head;
        int count = 0;
        while (current != nullptr && count < index)
        {
            current = current->next;
            count++;
        }
        if (current != nullptr)
        {
            current->firstName = contact.firstName;
            current->lastName = contact.lastName;
            current->phoneNumber = contact.phoneNumber;
            current->email = contact.email;
        }
    }

    void deleteContact(int index)
    {
        if (index == 0)
        {
            Contact *temp = head;
            head = head->next;
            delete temp;
        }
        else
        {
            Contact *current = head;
            int count = 0;
            while (current != nullptr && count < index - 1)
            {
                current = current->next;
                count++;
            }
            if (current != nullptr && current->next != nullptr)
            {
                Contact *temp = current->next;
                current->next = current->next->next;
                delete temp;
            }
        }
    }

    Contact *searchContacts(const string &searchTerm)
    {
        Contact *searchResults = nullptr;
        Contact **lastPtr = &searchResults;
        Contact *current = head;
        while (current != nullptr)
        {
            if (current->firstName.find(searchTerm) != string::npos ||
                current->lastName.find(searchTerm) != string::npos ||
                current->phoneNumber.find(searchTerm) != string::npos ||
                current->email.find(searchTerm) != string::npos)
            {
                Contact *newContact = new Contact(*current);
                newContact->next = nullptr;
                *lastPtr = newContact;
                lastPtr = &newContact->next;
            }
            current = current->next;
        }
        return searchResults;
    }

    void displayContacts()
    {
        Contact *current = head;
        while (current != nullptr)
        {
            cout << "Name: " << current->firstName << " " << current->lastName << endl;
            cout << "Phone Number: " << current->phoneNumber << endl;
            cout << "Email: " << current->email << endl;
            cout << endl;
            current = current->next;
        }
    }

    void sortContactsByName()
    {
        if (head == nullptr || head->next == nullptr)
        {
            return;
        }

        bool swapped;
        Contact *current;
        Contact *last = nullptr;

        do
        {
            swapped = false;
            current = head;

            while (current->next != last)
            {
                if (current->firstName > current->next->firstName)
                {
                    swapContacts(current, current->next);
                    swapped = true;
                }
                current = current->next;
            }
            last = current;
        } while (swapped);
    }

    void sortContactsByPhoneNumber()
    {
        if (head == nullptr || head->next == nullptr)
        {
            return;
        }

        bool swapped;
        Contact *current;
        Contact *last = nullptr;

        do
        {
            swapped = false;
            current = head;

            while (current->next != last)
            {
                if (current->phoneNumber > current->next->phoneNumber)
                {
                    swapContacts(current, current->next);
                    swapped = true;
                }
                current = current->next;
            }
            last = current;
        } while (swapped);
    }

    void swapContacts(Contact *contact1, Contact *contact2)
    {
        Contact temp = *contact1;
        *contact1 = *contact2;
        *contact2 = temp;

        // Correct the next pointers
        Contact *tempNext = contact1->next;
        contact1->next = contact2->next;
        contact2->next = tempNext;
    }
};

int main()
{
    PhoneDirectory phoneDirectory;

    int choice;
    do
    {
        cout << "Enter your choice: " << endl;
        cout << "1. Add a contact" << endl;
        cout << "2. Search contacts" << endl;
        cout << "3. Edit a contact" << endl;
        cout << "4. Delete a contact" << endl;
        cout << "5. Sort contacts by name" << endl;
        cout << "6. Sort contacts by phone number" << endl;
        cout << "7. Display Phone Numbers" << endl;
        cout << "8. Exit" << endl;
        cin >> choice;

        switch (choice)
        {
        case 1:
        {
            Contact contact;
            cout << "Enter the details for the new contact:" << endl;
            cout << "First Name: ";
            cin >> contact.firstName;
            cout << "Last Name: ";
            cin >> contact.lastName;
            cout << "Phone Number: ";
            cin >> contact.phoneNumber;
            cout << "Email: ";
            cin >> contact.email;

            phoneDirectory.addContact(contact);
            cout << "Contact added successfully!" << endl;
            break;
        }
        case 2:
        {
            string searchTerm;
            cout << "Enter the search term: ";
            cin >> searchTerm;

            Contact *searchResults = phoneDirectory.searchContacts(searchTerm);
            cout << "Search Results:" << endl;
            while (searchResults != nullptr)
            {
                cout << "Name: " << searchResults->firstName << " " << searchResults->lastName << endl;
                cout << "Phone Number: " << searchResults->phoneNumber << endl;
                cout << "Email: " << searchResults->email << endl;
                cout << endl;
                Contact *temp = searchResults;
                searchResults = searchResults->next;
                delete temp;
            }
            break;
        }
        case 3:
        {
            int editIndex;
            cout << "Enter the index of the contact to edit: ";
            cin >> editIndex;

            Contact newContact;
            cout << "Enter the new details for the contact:" << endl;
            cout << "First Name: ";
            cin >> newContact.firstName;
            cout << "Last Name: ";
            cin >> newContact.lastName;
            cout << "Phone Number: ";
            cin >> newContact.phoneNumber;
            cout << "Email: ";
            cin >> newContact.email;

            phoneDirectory.editContact(editIndex, newContact);
            cout << "Contact edited successfully!" << endl;
            break;
        }
        case 4:
        {
            int deleteIndex;
            cout << "Enter the index of the contact to delete: ";
            cin >> deleteIndex;

            phoneDirectory.deleteContact(deleteIndex);
            cout << "Contact deleted successfully!" << endl;
            break;
        }
        case 5:
        {
            phoneDirectory.sortContactsByName();
            cout << "Contacts sorted by name!" << endl;
            break;
        }
        case 6:
        {
            phoneDirectory.sortContactsByPhoneNumber();
            cout << "Contacts sorted by phone number!" << endl;
            break;
        }
        case 7:
            cout << "Contacts after modifications:" << endl;
            phoneDirectory.displayContacts();
            cout << endl;
            break;
        case 8:
            cout << "Exiting..." << endl;
            break;
        default:
            cout << "Invalid choice!" << endl;
            break;
        }

        cout << "Contacts after modifications:" << endl;
        phoneDirectory.displayContacts();
        cout << endl;

    } while (choice != 8);

    return 0;
}
