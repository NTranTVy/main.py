# main.py
import json
import os
from datetime import datetime
from statistics import mean

DATA_FILE = "students.json"


class Student:
    def __init__(self, student_id, name, math, literature, english):
        self.student_id = student_id
        self.name = name
        self.math = float(math)
        self.literature = float(literature)
        self.english = float(english)

    def average(self):
        return round((self.math + self.literature + self.english) / 3, 2)

    def classification(self):
        avg = self.average()
        if avg >= 8:
            return "Giỏi"
        elif avg >= 6.5:
            return "Khá"
        elif avg >= 5:
            return "Trung Bình"
        else:
            return "Yếu"

    def to_dict(self):
        return {
            "student_id": self.student_id,
            "name": self.name,
            "math": self.math,
            "literature": self.literature,
            "english": self.english,
        }


class StudentManager:
    def __init__(self):
        self.students = []
        self.load_data()

    def load_data(self):
        if os.path.exists(DATA_FILE):
            with open(DATA_FILE, "r", encoding="utf-8") as f:
                data = json.load(f)
                for item in data:
                    student = Student(
                        item["student_id"],
                        item["name"],
                        item["math"],
                        item["literature"],
                        item["english"],
                    )
                    self.students.append(student)

    def save_data(self):
        with open(DATA_FILE, "w", encoding="utf-8") as f:
            json.dump([s.to_dict() for s in self.students], f, indent=4, ensure_ascii=False)

    def add_student(self):
        print("\n=== THÊM HỌC SINH ===")
        student_id = input("Mã học sinh: ")
        name = input("Tên học sinh: ")
        math = float(input("Điểm Toán: "))
        literature = float(input("Điểm Văn: "))
        english = float(input("Điểm Anh: "))

        student = Student(student_id, name, math, literature, english)
        self.students.append(student)
        self.save_data()
        print("✔ Thêm thành công!")

    def list_students(self):
        print("\n=== DANH SÁCH HỌC SINH ===")
        if not self.students:
            print("Chưa có dữ liệu.")
            return

        for s in self.students:
            print(
                f"Mã: {s.student_id} | "
                f"Tên: {s.name} | "
                f"TB: {s.average()} | "
                f"Xếp loại: {s.classification()}"
            )

    def find_student(self, student_id):
        for s in self.students:
            if s.student_id == student_id:
                return s
        return None

    def delete_student(self):
        student_id = input("Nhập mã học sinh cần xoá: ")
        student = self.find_student(student_id)
        if student:
            self.students.remove(student)
            self.save_data()
            print("✔ Đã xoá!")
        else:
            print("Không tìm thấy học sinh.")

    def update_student(self):
        student_id = input("Nhập mã học sinh cần sửa: ")
        student = self.find_student(student_id)
        if student:
            print("Nhập điểm mới:")
            student.math = float(input("Toán: "))
            student.literature = float(input("Văn: "))
            student.english = float(input("Anh: "))
            self.save_data()
            print("✔ Cập nhật thành công!")
        else:
            print("Không tìm thấy học sinh.")

    def statistics(self):
        print("\n=== THỐNG KÊ ===")
        if not self.students:
            print("Không có dữ liệu.")
            return

        averages = [s.average() for s in self.students]
        print(f"Số học sinh: {len(self.students)}")
        print(f"Điểm trung bình lớp: {round(mean(averages), 2)}")
        print(f"Điểm cao nhất: {max(averages)}")
        print(f"Điểm thấp nhất: {min(averages)}")

    def export_report(self):
        filename = f"report_{datetime.now().strftime('%Y%m%d_%H%M%S')}.txt"
        with open(filename, "w", encoding="utf-8") as f:
            f.write("BÁO CÁO HỌC SINH\n")
            f.write("====================\n")
            for s in self.students:
                f.write(
                    f"{s.student_id} - {s.name} - "
                    f"TB: {s.average()} - "
                    f"{s.classification()}\n"
                )
        print(f"✔ Đã xuất báo cáo: {filename}")


def menu():
    manager = StudentManager()

    while True:
        print("\n========== QUẢN LÝ HỌC SINH ==========")
        print("1. Thêm học sinh")
        print("2. Danh sách học sinh")
        print("3. Sửa học sinh")
        print("4. Xoá học sinh")
        print("5. Thống kê")
        print("6. Xuất báo cáo")
        print("0. Thoát")
        choice = input("Chọn chức năng: ")

        if choice == "1":
            manager.add_student()
        elif choice == "2":
            manager.list_students()
        elif choice == "3":
            manager.update_student()
        elif choice == "4":
            manager.delete_student()
        elif choice == "5":
            manager.statistics()
        elif choice == "6":
            manager.export_report()
        elif choice == "0":
            print("Tạm biệt!")
            break
        else:
            print("Lựa chọn không hợp lệ.")


if __name__ == "__main__":
    menu()
