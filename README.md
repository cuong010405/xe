import ttkbootstrap as ttk
from ttkbootstrap.constants import *
from tkinter import messagebox

class Xe:
    def __init__(self, hang_san_xuat="", mau_sac="", bien_so=""):
        self.hang_san_xuat = hang_san_xuat
        self.mau_sac = mau_sac
        self.bien_so = bien_so

class Oto(Xe):
    def __init__(self, hang_san_xuat="", mau_sac="", bien_so="", so_cho_ngoi=0):
        super().__init__(hang_san_xuat, mau_sac, bien_so)
        self.so_cho_ngoi = so_cho_ngoi

class XeMay(Xe):
    def __init__(self, hang_san_xuat="", mau_sac="", bien_so="", loai_dong_co=""):
        super().__init__(hang_san_xuat, mau_sac, bien_so)
        self.loai_dong_co = loai_dong_co

class QuanLyXe:
    def __init__(self, root):
        self.root = root
        self.root.title("🚗 Quản lý xe 🏍")
        self.root.geometry("800x600")

        # Tạo Style mà không gán vào self.root
        self.style = ttk.Style(theme="solar")  # Giao diện đẹp hơn

        # Tiêu đề
        ttk.Label(root, text="📋 QUẢN LÝ XE", font=("Arial", 20, "bold"), bootstyle="primary").pack(pady=10)

        # Khung nhập liệu
        frame_nhap = ttk.Labelframe(root, text="➕ Nhập thông tin xe", bootstyle="info")
        frame_nhap.pack(pady=10, padx=10, fill="x")

        ttk.Label(frame_nhap, text="Hãng sản xuất:", bootstyle="light").grid(row=0, column=0, padx=5, pady=5)
        ttk.Label(frame_nhap, text="Màu sắc:", bootstyle="light").grid(row=1, column=0, padx=5, pady=5)
        ttk.Label(frame_nhap, text="Biển số:", bootstyle="light").grid(row=2, column=0, padx=5, pady=5)

        self.entry_hang = ttk.Entry(frame_nhap, bootstyle="success")
        self.entry_mau = ttk.Entry(frame_nhap, bootstyle="success")
        self.entry_bien = ttk.Entry(frame_nhap, bootstyle="success")

        self.entry_hang.grid(row=0, column=1, padx=5, pady=5)
        self.entry_mau.grid(row=1, column=1, padx=5, pady=5)
        self.entry_bien.grid(row=2, column=1, padx=5, pady=5)

        ttk.Label(frame_nhap, text="Loại xe:", bootstyle="light").grid(row=3, column=0, padx=5, pady=5)

        self.loai_xe_var = ttk.StringVar(value="Oto")
        self.option_loai_xe = ttk.Combobox(frame_nhap, textvariable=self.loai_xe_var, values=["Oto", "Xe Máy"])
        self.option_loai_xe.grid(row=3, column=1, padx=5, pady=5)

        ttk.Label(frame_nhap, text="Số chỗ / Động cơ:", bootstyle="light").grid(row=4, column=0, padx=5, pady=5)
        self.entry_thuoc_tinh = ttk.Entry(frame_nhap, bootstyle="success")
        self.entry_thuoc_tinh.grid(row=4, column=1, padx=5, pady=5)

        # Nút thêm xe
        ttk.Button(root, text="➕ Thêm xe", bootstyle="success-outline", command=self.them_xe).pack(pady=10)

        # Danh sách xe
        self.tree = ttk.Treeview(root, columns=("Loại xe", "Hãng", "Màu", "Biển số", "Chi tiết"), show="headings")
        self.tree.heading("Loại xe", text="Loại xe")
        self.tree.heading("Hãng", text="Hãng")
        self.tree.heading("Màu", text="Màu")
        self.tree.heading("Biển số", text="Biển số")
        self.tree.heading("Chi tiết", text="Chi tiết")
        self.tree.pack(pady=10)

        # Hiển thị tổng số xe
        self.label_tong_xe = ttk.Label(root, text="🚗 Tổng số xe: 0", font=("Arial", 12, "bold"), bootstyle="warning")
        self.label_tong_xe.pack(pady=5)

        # Khung tìm kiếm
        frame_timkiem = ttk.Labelframe(root, text="🔍 Tìm xe", bootstyle="info")
        frame_timkiem.pack(pady=10, padx=10, fill="x")

        ttk.Label(frame_timkiem, text="Nhập biển số:", bootstyle="light").grid(row=0, column=0, padx=5, pady=5)
        self.entry_timkiem = ttk.Entry(frame_timkiem, bootstyle="info")
        self.entry_timkiem.grid(row=0, column=1, padx=5, pady=5)
        ttk.Button(frame_timkiem, text="🔍 Tìm xe", bootstyle="primary-outline", command=self.tim_xe).grid(row=0, column=2, padx=5, pady=5)

    def them_xe(self):
        hang = self.entry_hang.get()
        mau = self.entry_mau.get()
        bien = self.entry_bien.get()
        loai_xe = self.loai_xe_var.get()
        chi_tiet = self.entry_thuoc_tinh.get()

        if not all([hang, mau, bien, chi_tiet]):
            messagebox.showwarning("⚠️ Lỗi", "Vui lòng nhập đầy đủ thông tin!")
            return

        if loai_xe == "Oto":
            oto = Oto(hang, mau, bien, int(chi_tiet))
            self.tree.insert("", "end", values=("Ô tô", hang, mau, bien, f"{chi_tiet} chỗ"))
        else:
            xemay = XeMay(hang, mau, bien, chi_tiet)
            self.tree.insert("", "end", values=("Xe máy", hang, mau, bien, f"Động cơ {chi_tiet}"))

        self.entry_hang.delete(0, ttk.END)
        self.entry_mau.delete(0, ttk.END)
        self.entry_bien.delete(0, ttk.END)
        self.entry_thuoc_tinh.delete(0, ttk.END)

        self.cap_nhat_tong_xe()

    def cap_nhat_tong_xe(self):
        tong_xe = len(self.tree.get_children())
        self.label_tong_xe.config(text=f"🚗 Tổng số xe: {tong_xe}")

    def tim_xe(self):
        bien_so = self.entry_timkiem.get()
        for item in self.tree.get_children():
            if self.tree.item(item, "values")[3] == bien_so:
                self.tree.selection_set(item)
                self.tree.focus(item)
                return
        messagebox.showinfo("🔍 Kết quả", "Không tìm thấy xe với biển số này.")

root = ttk.Window(themename="solar")
app = QuanLyXe(root)
root.mainloop()
