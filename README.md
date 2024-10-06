using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Data;
using System.Drawing;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Windows.Forms;

namespace Baitaptuan6
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
            dataGridView1.CellClick += dataGridView1_CellClick;
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            QLSinhVienEntities qLSinhVien = new QLSinhVienEntities();
            var sinhVienList = qLSinhVien.SinhViens.ToList();

            // Gán dữ liệu vào DataGridView
            dataGridView1.DataSource = sinhVienList;
            dataGridView1.Columns["MSSV"].Visible = false;
            LoadKhoa();
        }
        private void LoadKhoa()
        {
            cmbKhoa.Items.Add("Công Nghệ Thông Tin");
            cmbKhoa.Items.Add("Quản Trị Kinh Doanh");
            cmbKhoa.Items.Add("Ngoại Ngữ");
        }

        private void btnThem_Click(object sender, EventArgs e)
        {
            try
            {
                int maSoSV = int.Parse(txtMSSV.Text);
                string hoTen = txtHT.Text;
                string khoa = cmbKhoa.SelectedItem.ToString();
                float diemTB = float.Parse(txtDTB.Text);

                using (QLSinhVienEntities db = new QLSinhVienEntities())
                {
                    SinhVien newSinhVien = new SinhVien
                    {
                        MaSoSV = maSoSV,
                        HoTen = hoTen,
                        Khoa = khoa,
                        DiemTB = diemTB
                    };

                    db.SinhViens.Add(newSinhVien);
                    db.SaveChanges();
                }

                LoadData();
                MessageBox.Show("Thêm sinh viên thành công!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Information);
                ClearInput();
            }
            catch (Exception ex)
            {
                MessageBox.Show("Có lỗi xảy ra: " + ex.Message, "Lỗi", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
        }
        private void LoadData()
        {
            using (QLSinhVienEntities db = new QLSinhVienEntities())
            {
                var sinhVienList = db.SinhViens.ToList();
                dataGridView1.DataSource = sinhVienList;
            }
        }
        private void ClearInput()
        {
            txtMSSV.Text = "";
            txtHT.Text = "";
            cmbKhoa.SelectedIndex = -1; 
            txtDTB.Text = "";
        }

        private void btnXoa_Click(object sender, EventArgs e)
        {
            if (dataGridView1.CurrentRow != null)
            {
                int mssv = Convert.ToInt32(dataGridView1.CurrentRow.Cells["MaSoSV"].Value); 

                using (QLSinhVienEntities db = new QLSinhVienEntities())
                {
                    var sinhVien = db.SinhViens.FirstOrDefault(s => s.MaSoSV == mssv);

                    if (sinhVien != null)
                    {
                        // Xóa sinh viên
                        db.SinhViens.Remove(sinhVien);
                        db.SaveChanges();

                        LoadData();
                        MessageBox.Show("Đã xóa sinh viên thành công!", "Thông báo");
                    }
                    else
                    {
                        MessageBox.Show("Không tìm thấy sinh viên để xóa.", "Thông báo");
                    }
                }
            }
            else
            {
                MessageBox.Show("Vui lòng chọn một sinh viên để xóa.", "Thông báo");
            }

        }

        private void btnSua_Click(object sender, EventArgs e)
        {
            if (dataGridView1.CurrentRow != null)
            {
                int mssv = Convert.ToInt32(dataGridView1.CurrentRow.Cells["MaSoSV"].Value);

                using (QLSinhVienEntities db = new QLSinhVienEntities())
                {
                    var sinhVien = db.SinhViens.FirstOrDefault(s => s.MaSoSV == mssv);

                    if (sinhVien != null)
                    {
                        sinhVien.HoTen = txtHT.Text;
                        sinhVien.Khoa = cmbKhoa.SelectedItem.ToString();
                        sinhVien.DiemTB = float.Parse(txtDTB.Text);

                        db.SaveChanges();

                        LoadData();
                        MessageBox.Show("Đã sửa thông tin sinh viên thành công!", "Thông báo");
                        ClearInput();
                    }
                    else
                    {
                        MessageBox.Show("Không tìm thấy sinh viên để sửa!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Warning);
                    }
                }
            }
            else
            {
                MessageBox.Show("Vui lòng chọn sinh viên để sửa!", "Thông báo", MessageBoxButtons.OK, MessageBoxIcon.Warning);
            }
        }
        private void dataGridView1_CellClick(object sender, DataGridViewCellEventArgs e)
        {
            if (dataGridView1.CurrentRow != null)
            {
                txtMSSV.Text = dataGridView1.CurrentRow.Cells["MaSoSV"].Value.ToString();
                txtHT.Text = dataGridView1.CurrentRow.Cells["HoTen"].Value.ToString();
                cmbKhoa.SelectedItem = dataGridView1.CurrentRow.Cells["Khoa"].Value.ToString();
                txtDTB.Text = dataGridView1.CurrentRow.Cells["DiemTB"].Value.ToString();
            }
        }

        private void btnThoat_Click(object sender, EventArgs e)
        {
            Close();    
        }
    }
    
}
