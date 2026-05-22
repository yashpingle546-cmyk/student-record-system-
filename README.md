# student-record-system-
import csv
import os

DATA_FILE = os.path.join(os.path.dirname(__file__), 'students.csv')
FIELDNAMES = ['id', 'name', 'age', 'marks', 'subjects']


def load_records():
    records = []
    if not os.path.exists(DATA_FILE):
        return records
    with open(DATA_FILE, mode='r', newline='', encoding='utf-8') as csvfile:
        reader = csv.DictReader(csvfile, fieldnames=FIELDNAMES)
        records = [row for row in reader if row.get('id')]
    return records


def save_records(records):
    with open(DATA_FILE, mode='w', newline='', encoding='utf-8') as csvfile:
        writer = csv.DictWriter(csvfile, fieldnames=FIELDNAMES)
        for record in records:
            writer.writerow(record)


def generate_new_id(records):
    if not records:
        return '1'
    existing_ids = [int(record['id']) for record in records if record['id'].isdigit()]
    return str(max(existing_ids, default=0) + 1)


def display_records(records):
    if not records:
        print('No student records available.')
        return
    print('-' * 70)
    print(f"{'ID':<4}{'Name':<20}{'Age':<6}{'Marks':<8}{'Subjects'}")
    print('-' * 70)
    for record in records:
        print(f"{record['id']:<4}{record['name']:<20}{record['age']:<6}{record['marks']:<8}{record['subjects']}")
    print('-' * 70)


def prompt_record_data(existing=None):
    existing = existing or {}
    name = input(f"Name [{existing.get('name','')}]: ").strip() or existing.get('name', '')
    age = input(f"Age [{existing.get('age','')}]: ").strip() or existing.get('age', '')
    marks = input(f"Marks [{existing.get('marks','')}]: ").strip() or existing.get('marks', '')
    subjects = input(f"Subjects (comma separated) [{existing.get('subjects','')}]: ").strip() or existing.get('subjects', '')
    return {
        'name': name,
        'age': age,
        'marks': marks,
        'subjects': subjects,
    }


def add_record(records):
    print('\n=== Add Student Record ===')
    record = prompt_record_data()
    if not record['name']:
        print('Student name is required. Record not added.')
        return
    record['id'] = generate_new_id(records)
    records.append(record)
    save_records(records)
    print('Record added successfully.')


def find_record(records, student_id):
    for record in records:
        if record['id'] == student_id:
            return record
    return None


def edit_record(records):
    print('\n=== Edit Student Record ===')
    student_id = input('Enter student ID to edit: ').strip()
    record = find_record(records, student_id)
    if not record:
        print('No record found with that ID.')
        return
    updated = prompt_record_data(existing=record)
    record.update(updated)
    save_records(records)
    print('Record updated successfully.')


def delete_record(records):
    print('\n=== Delete Student Record ===')
    student_id = input('Enter student ID to delete: ').strip()
    record = find_record(records, student_id)
    if not record:
        print('No record found with that ID.')
        return
    records.remove(record)
    save_records(records)
    print('Record deleted successfully.')


def view_record(records):
    print('\n=== View Single Student ===')
    student_id = input('Enter student ID to view: ').strip()
    record = find_record(records, student_id)
    if not record:
        print('No record found with that ID.')
        return
    print('-' * 40)
    print(f"ID      : {record['id']}")
    print(f"Name    : {record['name']}")
    print(f"Age     : {record['age']}")
    print(f"Marks   : {record['marks']}")
    print(f"Subjects: {record['subjects']}")
    print('-' * 40)


def main_menu():
    records = load_records()
    while True:
        print('\nStudent Record Management System')
        print('1. View all records')
        print('2. Add a record')
        print('3. Edit a record')
        print('4. Delete a record')
        print('5. View single record')
        print('6. Exit')
        choice = input('Choose an option: ').strip()
        if choice == '1':
            display_records(records)
        elif choice == '2':
            add_record(records)
            records = load_records()
        elif choice == '3':
            edit_record(records)
            records = load_records()
        elif choice == '4':
            delete_record(records)
            records = load_records()
        elif choice == '5':
            view_record(records)
        elif choice == '6':
            print('Goodbye!')
            break
        else:
            print('Invalid choice. Please choose a valid option.')


if __name__ == '_main_':
    main_menu()