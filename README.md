# TASK_AUTOMATION
import tkinter as tk
from tkinter import filedialog, messagebox, scrolledtext
import re
import csv
from collections import Counter
import matplotlib.pyplot as plt

def extract_emails(text):
    return re.findall(r'[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+', text)

def open_file():
    file_path = filedialog.askopenfilename(filetypes=[("Text Files", "*.txt")])
    if not file_path:
        return
    
    with open(file_path, 'r', encoding='utf-8') as file:
        content = file.read()
    
    emails = extract_emails(content)
    email_output.delete('1.0', tk.END)

    if emails:
        email_output.insert(tk.END, '\n'.join(emails))
        show_email_stats(emails)
    else:
        email_output.insert(tk.END, "No emails found.")
        messagebox.showinfo("Info", "No email addresses found in the selected file.")

def export_emails():
    emails = email_output.get('1.0', tk.END).strip().split('\n')
    if not emails or emails == [""]:
        messagebox.showwarning("Warning", "No emails to export.")
        return

    export_path = filedialog.asksaveasfilename(defaultextension=".csv",
                                               filetypes=[("CSV files", "*.csv"), ("Text files", "*.txt")])
    if export_path:
        with open(export_path, 'w', newline='') as f:
            writer = csv.writer(f)
            writer.writerow(["Email Address"])
            for email in emails:
                writer.writerow([email])
        messagebox.showinfo("Exported", f"Emails exported to {export_path}")

def show_email_stats(emails):
    domains = [email.split('@')[1] for email in emails]
    domain_counts = Counter(domains)

    plt.figure(figsize=(6, 4))
    plt.bar(domain_counts.keys(), domain_counts.values(), color='skyblue')
    plt.xlabel('Domain')
    plt.ylabel('Count')
    plt.title('Email Domain Distribution')
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()

# GUI Setup
root = tk.Tk()
root.title("Email Extractor")
root.geometry("600x500")
root.config(bg="lightblue")

tk.Label(root, text="📧 Email Extractor Tool", font=("Helvetica", 16, "bold"), bg="lightblue").pack(pady=10)

tk.Button(root, text="Open .txt File", command=open_file, bg="#3498db", fg="white", padx=10, pady=5).pack(pady=5)

email_output = scrolledtext.ScrolledText(root, height=15, width=70)
email_output.pack(pady=10)

tk.Button(root, text="Export Emails", command=export_emails, bg="#27ae60", fg="white", padx=10, pady=5).pack(pady=5)

root.mainloop()
