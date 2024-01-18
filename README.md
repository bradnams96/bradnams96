import tkinter as tk
from tkinter import messagebox

def add_staff_member(event=None):
    staff_members = staff_entry.get().split(';')
    staff_members = [staff.strip() for staff in staff_members if staff.strip()]
    
    for staff_member in staff_members:
        if staff_member:
            staff_members_listbox.insert(tk.END, staff_member)
            staff_entry.delete(0, tk.END)

def add_subject_taught(event=None):
    subjects_taught = subject_entry.get().split(';')
    subjects_taught = [subject.strip() for subject in subjects_taught if subject.strip()]
    
    for subject_taught in subjects_taught:
        if subject_taught:
            subjects_listbox.insert(tk.END, subject_taught)
            subject_entry.delete(0, tk.END)

def add_course(event=None):
    courses = course_entry.get().split(';')
    courses = [course.strip() for course in courses if course.strip()]
    
    for course in courses:
        if course:
            courses_listbox.insert(tk.END, course)
            course_entry.delete(0, tk.END)
    
    update_course_dropdown()

def assign_staff_members():
    selected_subject = subjects_listbox.get(tk.ACTIVE)
    selected_staff_indices = staff_members_listbox.curselection()

    if selected_subject and selected_staff_indices:
        assigned_staff_members = [staff_members_listbox.get(index) for index in selected_staff_indices]

        for staff_member in assigned_staff_members:
            assignment = f"{staff_member} can teach {selected_subject}"
            assignments_listbox.insert(tk.END, assignment)
    else:
        messagebox.showwarning("Selection Error", "Please select a subject and at least one staff member.")

def assign_subject_to_course():
    selected_subject_indices = subjects_listbox.curselection()
    selected_course = course_var.get()

    if selected_subject_indices and selected_course:
        selected_subjects = [subjects_listbox.get(index) for index in selected_subject_indices]

        for subject in selected_subjects:
            assignment = f"{subject} is assigned to {selected_course}"
            subject_course_assignments_listbox.insert(tk.END, assignment)

        update_course_subjects(selected_course)
    else:
        messagebox.showwarning("Selection Error", "Please select at least one subject and a course.")

def update_course_dropdown():
    menu = course_dropdown['menu']
    menu.delete(0, 'end')

    for course in courses_listbox.get(0, tk.END):
        menu.add_command(label=course, command=tk._setit(course_var, course))

def update_course_subjects(course):
    course_subjects_listbox.delete(0, tk.END)
    for assignment in subject_course_assignments_listbox.get(0, tk.END):
        if assignment.endswith(f"is assigned to {course}"):
            course_subjects_listbox.insert(tk.END, assignment.replace(f" is assigned to {course}", ""))

# Create the main window
root = tk.Tk()
root.title("Subject-Staff Assignment")
root.geometry("1400x400")  # Set the initial size of the window

# Staff Members Section
staff_label = tk.Label(root, text="Enter Staff Members (Separate entries with ';'):")
staff_label.grid(row=0, column=0, pady=5, padx=10, sticky="w")

staff_entry = tk.Entry(root)
staff_entry.grid(row=1, column=0, pady=5, padx=10, sticky="w")
staff_entry.bind('<Return>', add_staff_member)  # Bind Return key to the function

add_staff_button = tk.Button(root, text="Add Staff Member", command=add_staff_member)
add_staff_button.grid(row=2, column=0, pady=5, padx=10, sticky="w")

staff_members_listbox = tk.Listbox(root, selectmode=tk.MULTIPLE)
staff_members_listbox.grid(row=3, column=0, pady=5, padx=10, sticky="w")

# Subjects Taught Section
subject_label = tk.Label(root, text="Enter Subjects Taught (Separate entries with ';'):")
subject_label.grid(row=0, column=1, pady=5, padx=10, sticky="w")

subject_entry = tk.Entry(root)
subject_entry.grid(row=1, column=1, pady=5, padx=10, sticky="w")
subject_entry.bind('<Return>', add_subject_taught)  # Bind Return key to the function

add_subject_button = tk.Button(root, text="Add Subject Taught", command=add_subject_taught)
add_subject_button.grid(row=2, column=1, pady=5, padx=10, sticky="w")

subjects_listbox = tk.Listbox(root, selectmode=tk.MULTIPLE)
subjects_listbox.grid(row=3, column=1, pady=5, padx=10, sticky="w")

# Courses Section
course_label = tk.Label(root, text="Enter Courses (Separate entries with ';'):")
course_label.grid(row=0, column=2, pady=5, padx=10, sticky="w")

course_entry = tk.Entry(root)
course_entry.grid(row=1, column=2, pady=5, padx=10, sticky="w")
course_entry.bind('<Return>', add_course)  # Bind Return key to the function

add_course_button = tk.Button(root, text="Add Course", command=add_course)
add_course_button.grid(row=2, column=2, pady=5, padx=10, sticky="w")

courses_listbox = tk.Listbox(root)
courses_listbox.grid(row=3, column=2, pady=5, padx=10, sticky="w")

# Course Dropdown Section
course_var = tk.StringVar(root)  # Add this line to create a dummy variable
course_var.set("Select Course")  # Set an initial value

course_dropdown_label = tk.Label(root, text="Select Course:")
course_dropdown_label.grid(row=4, column=0, pady=5, padx=10, sticky="w")

course_dropdown = tk.OptionMenu(root, course_var, "")
course_dropdown.grid(row=5, column=0, pady=5, padx=10, sticky="w")
course_dropdown.config(width=15)

# Course Subjects Section
course_subjects_label = tk.Label(root, text="Course Subjects:")
course_subjects_label.grid(row=4, column=1, pady=5, padx=10, sticky="w")

course_subjects_listbox = tk.Listbox(root)
course_subjects_listbox.grid(row=5, column=1, pady=5, padx=10, sticky="w")

# Course Days Section
course_days_label = tk.Label(root, text="Course Days:")
course_days_label.grid(row=4, column=2, pady=5, padx=5, sticky="w")

course_days_var = [tk.IntVar() for _ in range(5)]  # Monday to Friday
course_days_checkboxes = [
    tk.Checkbutton(root, text=day, variable=var)
    for day, var in zip(["Monday", "Tuesday", "Wednesday", "Thursday", "Friday"], course_days_var)
]

for i, checkbox in enumerate(course_days_checkboxes):
    checkbox.grid(row=i + 5, column=2, pady=1, padx=5, sticky="w")

# Subject-Staff Assignment Section
assign_button = tk.Button(root, text="Assign Staff Members to Subject", command=assign_staff_members)
assign_button.grid(row=0, column=3, rowspan=2, pady=5, padx=10, sticky="w")

assignments_label = tk.Label(root, text="Subject-Staff Assignments:")
assignments_label.grid(row=2, column=3, pady=5, padx=10, sticky="w")

assignments_listbox = tk.Listbox(root)
assignments_listbox.grid(row=3, column=3, pady=5, padx=10, sticky="w")

# Subject-Course Assignment Section
assign_subject_course_button = tk.Button(root, text="Assign Subject to Course", command=assign_subject_to_course)
assign_subject_course_button.grid(row=0, column=4, rowspan=2, pady=5, padx=10, sticky="w")

subject_course_assignments_label = tk.Label(root, text="Subject-Course Assignments:")
subject_course_assignments_label.grid(row=2, column=4, pady=5, padx=10, sticky="w")

subject_course_assignments_listbox = tk.Listbox(root)
subject_course_assignments_listbox.grid(row=3, column=4, pady=5, padx=10, sticky="w")

# Start the GUI main loop
root.mainloop()
