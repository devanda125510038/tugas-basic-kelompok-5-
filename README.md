# APLIKASI MANAJEMEN & ANALISIS PERFORMA ATLET
# Topik 3 – Final Project: Modularisasi, Persistensi Data & Implementasi Aplikasi Utuh Berbasis Menu

import os
import csv
from datetime import date
# Modul utilitas berisi semua fungsi kalkulasi & evaluasi
# untuk Aplikasi Manajemen & Analisis Performa Atlet


# 1. FUNGSI IMT

def hitung_imt(berat, tinggi_cm):
    """
    Menghitung Indeks Massa Tubuh (IMT).

    Parameters:
        berat     (float): Berat badan dalam kg
        tinggi_cm (float): Tinggi badan dalam cm

    Returns:
        float: Nilai IMT
    """
    tinggi_m = tinggi_cm / 100
    return berat / (tinggi_m ** 2)

def kategori_imt(imt):
    """
    Menentukan kategori IMT atlet.

    Parameters:
        imt (float): Nilai IMT hasil perhitungan

    Returns:
        str: Kategori IMT (Underweight/Normal/Overweight/Obesitas)
    """
    if imt < 18.5:
        return "Underweight"
    elif imt < 25.0:
        return "Normal"
    elif imt < 30.0:
        return "Overweight"
    else:
        return "Obesitas"


# 2. FUNGSI DETAK JANTUNG & ZONA LATIHAN

def hitung_hrmax(usia):
    """
    Menghitung detak jantung maksimum (HRmax).

    Parameters:
        usia (int): Usia atlet dalam tahun

    Returns:
        int: Nilai HRmax dalam bpm
    """
    return 220 - usia


def zona_latihan(hrmax):
    """
    Menghitung zona latihan berdasarkan HRmax.

    Parameters:
        hrmax (int): Detak jantung maksimum dalam bpm

    Returns:
        dict: Dictionary berisi zona ringan, sedang, dan berat
    """
    return {
        "ringan" : round(0.50 * hrmax, 2),
        "sedang" : round(0.70 * hrmax, 2),
        "berat"  : round(0.85 * hrmax, 2),
    }

# 3. FUNGSI KALORI

def hitung_kalori(berat, durasi, intensitas="sedang"):
    """
    Menghitung estimasi kalori terbakar saat latihan.

    Parameters:
        berat      (float): Berat badan atlet dalam kg
        durasi     (int)  : Durasi latihan dalam menit
        intensitas (str)  : Intensitas latihan – 'ringan'/'sedang'/'berat'
                            (default: 'sedang')

    Returns:
        float: Estimasi kalori terbakar (kkal)
    """
    met = {"ringan": 3.5, "sedang": 7.0, "berat": 10.5}
    return round(met.get(intensitas, 7.0) * berat * (durasi / 60), 2)
# 4. FUNGSI EVALUASI PERFORMA

def evaluasi_performa(detak_istirahat, sistolik, diastolik, vo2max, imt_val):
    """
    Mengevaluasi kondisi fisik atlet secara menyeluruh dan
    memberikan rekomendasi latihan.

    Parameters:
        detak_istirahat (int)  : Detak jantung istirahat (bpm)
        sistolik        (int)  : Tekanan darah sistolik (mmHg)
        diastolik       (int)  : Tekanan darah diastolik (mmHg)
        vo2max          (float): Nilai VO2Max atlet
        imt_val         (float): Nilai IMT atlet

    Returns:
        dict: Hasil evaluasi berisi status tiap indikator & rekomendasi
    """
    hasil = {}
    # Klasifikasi detak jantung
    if detak_istirahat < 60:
        hasil["detak"] = "Bradikardia"
    elif detak_istirahat <= 100:
        hasil["detak"] = "Normal"
    else:
        hasil["detak"] = "Takikardia"

    # Klasifikasi tekanan darah
    if sistolik < 120 and diastolik < 80:
        hasil["tekanan"] = "Normal"
    elif sistolik < 130 and diastolik < 80:
        hasil["tekanan"] = "Prehipertensi"
    elif sistolik < 140 or diastolik < 90:
        hasil["tekanan"] = "Hipertensi Level 1"
    else:
        hasil["tekanan"] = "Hipertensi Level 2"

    # Klasifikasi VO2Max
    if vo2max < 28:
        hasil["vo2max"] = "Sangat Buruk"
    elif vo2max < 34:
        hasil["vo2max"] = "Buruk"
    elif vo2max < 42:
        hasil["vo2max"] = "Cukup"
    elif vo2max < 52:
        hasil["vo2max"] = "Baik"
    elif vo2max < 60:
        hasil["vo2max"] = "Sangat Baik"
    else:
        hasil["vo2max"] = "Superior"

    # Klasifikasi IMT
    hasil["imt_kat"] = kategori_imt(imt_val)

    # Rekomendasi latihan
    rekomendasi = []
    if hasil["detak"] == "Bradikardia":
        rekomendasi.append("Konsultasi dokter sebelum latihan intensitas tinggi")
    elif hasil["detak"] == "Takikardia":
        rekomendasi.append("Hindari latihan intensitas tinggi, fokus peregangan")

    if hasil["tekanan"] == "Normal":
        rekomendasi.append("Latihan aerobik intensitas sedang (3-5x/minggu)")
    elif hasil["tekanan"] == "Prehipertensi":
        rekomendasi.append("Latihan aerobik intensitas sedang (3-5x/minggu)")
        rekomendasi.append("Kontrol asupan garam, pantau tekanan darah")
    elif "Hipertensi" in hasil["tekanan"]:
        rekomendasi.append("Latihan ringan (jalan kaki, yoga), hindari angkat beban berat")
        rekomendasi.append("Pantau tekanan darah sebelum & sesudah latihan")

    if hasil["vo2max"] in ["Sangat Buruk", "Buruk"]:
        rekomendasi.append("Tingkatkan kardio: jalan cepat/bersepeda 20-30 menit/hari")
    elif hasil["vo2max"] in ["Cukup", "Baik"]:
        rekomendasi.append("Pertahankan rutinitas aerobik, tambah variasi HIIT")
    else:
        rekomendasi.append("Pertahankan performa, fokus ke latihan teknik & kekuatan")

    if hasil["imt_kat"] == "Underweight":
        rekomendasi.append("Tingkatkan asupan kalori & latihan kekuatan (weight training)")
    elif hasil["imt_kat"] in ["Overweight", "Obesitas"]:
        rekomendasi.append("Kombinasikan kardio & diet seimbang untuk turunkan berat badan")

    if not rekomendasi:
        rekomendasi.append("Kondisi prima! Pertahankan pola latihan dan gaya hidup sehat")

    hasil["rekomendasi"] = rekomendasi
    return hasil
# 5. HELPER: Validasi input angka

def input_angka(prompt, tipe=float, min_val=None, max_val=None):
    """
    Meminta input angka dari pengguna dengan validasi.

    Parameters:
        prompt  (str)  : Teks prompt yang ditampilkan
        tipe    (type) : Tipe data yang diharapkan (int/float, default float)
        min_val        : Nilai minimum yang diizinkan (opsional)
        max_val        : Nilai maksimum yang diizinkan (opsional)

    Returns:
        int/float: Nilai yang diinput pengguna setelah validasi
    """
    while True:
        try:
            nilai = tipe(input(prompt))
            if min_val is not None and nilai < min_val:
                print(f"  ⚠  Nilai minimal adalah {min_val}. Coba lagi.")
                continue
            if max_val is not None and nilai > max_val:
                print(f"  ⚠  Nilai maksimal adalah {max_val}. Coba lagi.")
                continue
            return nilai
        except ValueError:
            print(f"  ⚠  Input tidak valid. Masukkan angka.")
# APLIKASI MANAJEMEN & ANALISIS PERFORMA ATLET
# Topik 3 – Final Project: Modularisasi, Persistensi Data & Implementasi Aplikasi Utuh Berbasis Menu

import os
import csv
from datetime import date

# Import semua fungsi dari modul utilitas
from utilitas_atlet import (
    hitung_imt,
    kategori_imt,
    hitung_hrmax,
    zona_latihan,
    hitung_kalori,
    evaluasi_performa,
    input_angka,
)
# KONSTANTA FILE
FILE_ATLET   = "data_atlet.csv"
FILE_LATIHAN = "data_latihan.csv"
FILE_LAPORAN = "laporan_ringkasan.txt"

HEADER_ATLET   = ["nama", "usia", "tinggi_cm", "berat_kg",
                  "cabang", "detak_istirahat", "sistolik",
                  "diastolik", "vo2max"]
HEADER_LATIHAN = ["tanggal", "nama", "jenis_latihan",
                  "durasi_menit", "kalori", "hr_rata", "intensitas"]
# UTILITAS TAMPILAN

def garis(n=45, char="="):
    return char * n

def header(judul):
    print()
    print(garis())
    print(f"  {judul}")
    print(garis())

def jeda():
    input("\n  [Tekan Enter untuk kembali ke menu...]")
# FILE HANDLING – fungsi baca/tulis CSV

def inisialisasi_file():
    """Buat file CSV jika belum ada, lengkap dengan header."""
    for path, header_row in [(FILE_ATLET, HEADER_ATLET),
                              (FILE_LATIHAN, HEADER_LATIHAN)]:
        if not os.path.exists(path):
            with open(path, "w", newline="", encoding="utf-8") as f:
                writer = csv.writer(f)
                writer.writerow(header_row)
def baca_csv(path, header_row):
    """
    Membaca file CSV ke dalam list of dict.

    Parameters:
        path       (str) : Path file CSV
        header_row (list): Daftar nama kolom

    Returns:
        list[dict]: Data dari file, atau list kosong jika file tidak ada
    """
    data = []
    try:
        with open(path, "r", newline="", encoding="utf-8") as f:
            reader = csv.DictReader(f)
            for baris in reader:
                data.append(baris)
    except FileNotFoundError:
        print(f"  ⚠  File '{path}' tidak ditemukan.")
    except Exception as e:
        print(f"  ⚠  Gagal membaca file: {e}")
    return data
def tulis_csv(path, header_row, data):
    """
    Menulis ulang seluruh data ke file CSV (overwrite).

    Parameters:
        path       (str)       : Path file CSV
        header_row (list)      : Daftar nama kolom
        data       (list[dict]): Data yang akan ditulis
    """
    try:
        with open(path, "w", newline="", encoding="utf-8") as f:
            writer = csv.DictWriter(f, fieldnames=header_row)
            writer.writeheader()
            writer.writerows(data)
        print(f"  ✔ Data berhasil disimpan ke '{path}'.")
    except Exception as e:
        print(f"  ⚠  Gagal menyimpan file: {e}")
def append_csv(path, header_row, baris_baru):
    """
    Menambahkan satu baris ke file CSV tanpa menghapus data lama.

    Parameters:
        path      (str) : Path file CSV
        header_row(list): Daftar nama kolom
        baris_baru(dict): Data baris baru yang akan ditambahkan
    """
    try:
        file_baru = not os.path.exists(path)
        with open(path, "a", newline="", encoding="utf-8") as f:
            writer = csv.DictWriter(f, fieldnames=header_row)
            if file_baru:
                writer.writeheader()
            writer.writerow(baris_baru)
        print(f"  ✔ Data berhasil ditambahkan ke '{path}'.")
    except Exception as e:
        print(f"  ⚠  Gagal menambah data: {e}")
# MENU 1 – REGISTRASI ATLET BARU

def registrasi_atlet():
    header("📋  REGISTRASI ATLET BARU")

    nama   = input("  Nama atlet               : ").strip()
    if not nama:
        print("  ⚠  Nama tidak boleh kosong.")
        return

    usia   = input_angka("  Usia (tahun)             : ", int, 10, 80)
    tinggi = input_angka("  Tinggi badan (cm)        : ", float, 100, 250)
    berat  = input_angka("  Berat badan (kg)         : ", float, 20, 300)
    cabang = input("  Cabang olahraga          : ").strip()
    detak  = input_angka("  Detak jantung istirahat  : ", int, 30, 200)
    sis    = input_angka("  Tekanan darah sistolik   : ", int, 60, 250)
    dias   = input_angka("  Tekanan darah diastolik  : ", int, 40, 150)
    vo2    = input_angka("  VO2Max                   : ", float, 10, 90)

    baris = {
        "nama": nama, "usia": usia,
        "tinggi_cm": tinggi, "berat_kg": berat,
        "cabang": cabang, "detak_istirahat": detak,
        "sistolik": sis, "diastolik": dias, "vo2max": vo2,
    }
    append_csv(FILE_ATLET, HEADER_ATLET, baris)

    # Tampilkan ringkasan
    imt_val = hitung_imt(berat, tinggi)
    hrmax   = hitung_hrmax(usia)
    print(f"\n  IMT    : {imt_val:.2f} → {kategori_imt(imt_val)}")
    print(f"  HRmax  : {hrmax} bpm")
    jeda()
# MENU 2 – INPUT DATA LATIHAN

def input_data_latihan():
    header("🏃  INPUT DATA LATIHAN")

    nama      = input("  Nama atlet        : ").strip()
    tgl       = input(f"  Tanggal (YYYY-MM-DD) [{date.today()}]: ").strip()
    if not tgl:
        tgl = str(date.today())

    jenis     = input("  Jenis latihan     : ").strip()
    intensitas= input("  Intensitas (ringan/sedang/berat) [sedang]: ").strip().lower()
    if intensitas not in ["ringan", "sedang", "berat"]:
        intensitas = "sedang"

    durasi    = input_angka("  Durasi (menit)    : ", int, 1, 600)
    hr_rata   = input_angka("  HR rata-rata (bpm): ", int, 30, 220)

    # Cari berat atlet dari file untuk estimasi kalori
    semua_atlet = baca_csv(FILE_ATLET, HEADER_ATLET)
    berat       = 70.0   # default
    for a in semua_atlet:
        if a["nama"].lower() == nama.lower():
            berat = float(a["berat_kg"])
            break

    # Gunakan fungsi dari modul utilitas (keyword argument)
    kalori = hitung_kalori(berat=berat, durasi=durasi, intensitas=intensitas)

    baris = {
        "tanggal": tgl, "nama": nama,
        "jenis_latihan": jenis, "durasi_menit": durasi,
        "kalori": kalori, "hr_rata": hr_rata, "intensitas": intensitas,
    }
    append_csv(FILE_LATIHAN, HEADER_LATIHAN, baris)
    print(f"  Estimasi kalori terbakar: {kalori} kkal")
    jeda()
# MENU 3 – EVALUASI KONDISI FISIK

def evaluasi_kondisi():
    header("❤  EVALUASI KONDISI FISIK")

    nama  = input("  Nama atlet (atau Enter untuk input manual): ").strip()

    # Coba ambil data dari file
    semua = baca_csv(FILE_ATLET, HEADER_ATLET)
    data_a = None
    for a in semua:
        if a["nama"].lower() == nama.lower():
            data_a = a
            break

    if data_a:
        detak = int(data_a["detak_istirahat"])
        sis   = int(data_a["sistolik"])
        dias  = int(data_a["diastolik"])
        vo2   = float(data_a["vo2max"])
        imt_v = hitung_imt(float(data_a["berat_kg"]), float(data_a["tinggi_cm"]))
        print(f"  ✔ Data atlet '{nama}' dimuat dari file.")
    else:
        print("  Data tidak ditemukan, masukkan manual:")
        detak = input_angka("  Detak jantung istirahat (bpm): ", int, 30, 200)
        sis   = input_angka("  Tekanan darah sistolik       : ", int, 60, 250)
        dias  = input_angka("  Tekanan darah diastolik      : ", int, 40, 150)
        vo2   = input_angka("  VO2Max                       : ", float, 10, 90)
        imt_v = input_angka("  IMT                          : ", float, 10, 50)

    # Panggil fungsi evaluasi dari modul utilitas
    hasil = evaluasi_performa(detak, sis, dias, vo2, imt_v)

    print(f"\n  {'Detak Jantung Istirahat':<26}: {detak} bpm  → {hasil['detak']}")
    print(f"  {'Tekanan Darah':<26}: {sis}/{dias}   → {hasil['tekanan']}")
    print(f"  {'VO2Max':<26}: {vo2:<6} → {hasil['vo2max']}")
    print(f"  {'IMT':<26}: {imt_v:<6.2f} → {hasil['imt_kat']}")
    print(f"\n  🏋 REKOMENDASI LATIHAN:")
    for r in hasil["rekomendasi"]:
        print(f"    ✔ {r}")
    jeda()

# MENU 4 – ANALISIS PROGRES MINGGUAN

def analisis_progres():
    header("📊  ANALISIS PROGRES MINGGUAN")

    nama = input("  Nama atlet: ").strip()
    semua_latihan = baca_csv(FILE_LATIHAN, HEADER_LATIHAN)

    # Filter data milik atlet ini
    data_atlet = [d for d in semua_latihan
                  if d["nama"].lower() == nama.lower()]

    if not data_atlet:
        print(f"  ⚠  Tidak ada data latihan untuk '{nama}'.")
        jeda()
        return

    # Hitung statistik
    total_durasi  = sum(int(d["durasi_menit"]) for d in data_atlet)
    total_kalori  = sum(float(d["kalori"]) for d in data_atlet)
    rata_kalori   = total_kalori / len(data_atlet)
    hr_tertinggi  = max(int(d["hr_rata"]) for d in data_atlet)
    sesi_terbanyak= max(set(d["jenis_latihan"] for d in data_atlet),
                        key=lambda j: sum(1 for d in data_atlet
                                          if d["jenis_latihan"] == j))

    # Tabel
    print(f"\n  {'No':<3} | {'Tanggal':<12} | {'Jenis':<14} | "
          f"{'Durasi':>7} | {'Kalori':>7} | {'HR':>6} | Grafik")
    print("  " + "-"*3 + "-+-" + "-"*12 + "-+-" + "-"*14 + "-+-" +
          "-"*7 + "-+-" + "-"*7 + "-+-" + "-"*6 + "-+-" + "-"*15)

    for i, d in enumerate(data_atlet, 1):
        bar = "🟦" * min(int(float(d["durasi_menit"]) / 10), 10)
        print(f"  {i:<3} | {d['tanggal']:<12} | {d['jenis_latihan']:<14} | "
              f"{d['durasi_menit']:>5} m | {float(d['kalori']):>7.1f} | "
              f"{d['hr_rata']:>6} | {bar}")

    print(f"\n  Total Durasi  : {total_durasi} menit")
    print(f"  Total Kalori  : {total_kalori:.1f} kkal")
    print(f"  Rata-rata Kal : {rata_kalori:.2f} kkal/sesi")
    print(f"  HR Tertinggi  : {hr_tertinggi} bpm")
    print(f"  Latihan Favorit: {sesi_terbanyak}")
    jeda()
# MENU 5 – CARI DATA ATLET

def cari_data_atlet():
    header("🔍  CARI DATA ATLET")

    print("  Cari berdasarkan:")
    print("  [1] Nama atlet")
    print("  [2] Tanggal latihan")
    pilihan = input("  Pilihan: ").strip()

    semua_latihan = baca_csv(FILE_LATIHAN, HEADER_LATIHAN)

    if pilihan == "1":
        kata = input("  Masukkan nama: ").strip().lower()
        hasil = [d for d in semua_latihan if kata in d["nama"].lower()]
    elif pilihan == "2":
        tgl   = input("  Masukkan tanggal (YYYY-MM-DD): ").strip()
        hasil = [d for d in semua_latihan if d["tanggal"] == tgl]
    else:
        print("  ⚠  Pilihan tidak valid.")
        jeda()
        return

    if not hasil:
        print("  ⚠  Data tidak ditemukan.")
    else:
        print(f"\n  Ditemukan {len(hasil)} data:\n")
        print(f"  {'Tanggal':<12} | {'Nama':<14} | {'Jenis':<14} | "
              f"{'Durasi':>7} | {'Kalori':>7}")
        print("  " + "-"*65)
        for d in hasil:
            print(f"  {d['tanggal']:<12} | {d['nama']:<14} | "
                  f"{d['jenis_latihan']:<14} | {d['durasi_menit']:>5} m | "
                  f"{float(d['kalori']):>7.1f}")
    jeda()
# MENU 6 – SIMPAN DATA KE FILE (re-write)

def simpan_data():
    header("💾  SIMPAN DATA KE FILE")
    print("  Menyimpan ulang semua data ke file CSV...")

    atlet   = baca_csv(FILE_ATLET, HEADER_ATLET)
    latihan = baca_csv(FILE_LATIHAN, HEADER_LATIHAN)

    tulis_csv(FILE_ATLET, HEADER_ATLET, atlet)
    tulis_csv(FILE_LATIHAN, HEADER_LATIHAN, latihan)
    jeda()
# MENU 7 – MUAT DATA DARI FILE

def muat_data():
    header("📂  MUAT DATA DARI FILE")

    atlet   = baca_csv(FILE_ATLET, HEADER_ATLET)
    latihan = baca_csv(FILE_LATIHAN, HEADER_LATIHAN)

    print(f"  ✔ {len(atlet)} data atlet dimuat dari '{FILE_ATLET}'.")
    print(f"  ✔ {len(latihan)} sesi latihan dimuat dari '{FILE_LATIHAN}'.")

    if atlet:
        print("\n  Daftar Atlet Terdaftar:")
        for i, a in enumerate(atlet, 1):
            imt_v = hitung_imt(float(a["berat_kg"]), float(a["tinggi_cm"]))
            print(f"    {i}. {a['nama']:<20} | {a['cabang']:<12} | "
                  f"IMT: {imt_v:.2f} ({kategori_imt(imt_v)})")
    jeda()
# MENU 8 – EKSPOR LAPORAN RINGKASAN

def ekspor_laporan():
    header("📄  EKSPOR LAPORAN RINGKASAN")

    atlet   = baca_csv(FILE_ATLET, HEADER_ATLET)
    latihan = baca_csv(FILE_LATIHAN, HEADER_LATIHAN)

    try:
        with open(FILE_LAPORAN, "w", encoding="utf-8") as f:
            f.write("=" * 50 + "\n")
            f.write("   LAPORAN RINGKASAN PERFORMA TIM OLAHRAGA\n")
            f.write(f"   ITERA 2026 | Tanggal: {date.today()}\n")
            f.write("=" * 50 + "\n\n")

            f.write(f"Total Atlet Terdaftar : {len(atlet)}\n")
            f.write(f"Total Sesi Latihan    : {len(latihan)}\n\n")

            f.write("── PROFIL ATLET ──\n")
            for a in atlet:
                imt_v  = hitung_imt(float(a["berat_kg"]), float(a["tinggi_cm"]))
                hrmax  = hitung_hrmax(int(a["usia"]))
                zona   = zona_latihan(hrmax)
                hasil  = evaluasi_performa(
                    int(a["detak_istirahat"]), int(a["sistolik"]),
                    int(a["diastolik"]), float(a["vo2max"]), imt_v
                )
                f.write(f"\nNama   : {a['nama']}\n")
                f.write(f"Usia   : {a['usia']} th | Cabang: {a['cabang']}\n")
                f.write(f"IMT    : {imt_v:.2f} ({hasil['imt_kat']})\n")
                f.write(f"HRmax  : {hrmax} bpm\n")
                f.write(f"Zona   : Ringan {zona['ringan']} | "
                        f"Sedang {zona['sedang']} | Berat {zona['berat']} bpm\n")
                f.write(f"Status : DJ={hasil['detak']} | "
                        f"TD={hasil['tekanan']} | VO2={hasil['vo2max']}\n")
                f.write("Rekomendasi:\n")
                for r in hasil["rekomendasi"]:
                    f.write(f"  - {r}\n")

            if latihan:
                total_kal = sum(float(d["kalori"]) for d in latihan)
                total_dur = sum(int(d["durasi_menit"]) for d in latihan)
                f.write("\n── RINGKASAN LATIHAN TIM ──\n")
                f.write(f"Total Durasi : {total_dur} menit\n")
                f.write(f"Total Kalori : {total_kal:.1f} kkal\n")

        print(f"  ✔ Laporan berhasil diekspor ke '{FILE_LAPORAN}'.")
    except Exception as e:
        print(f"  ⚠  Gagal ekspor laporan: {e}")

    jeda()
# MENU UTAMA

def tampilkan_menu():
    print()
    print(garis(48))
    print("   APLIKASI MANAJEMEN PERFORMA ATLET")
    print("   Rekayasa Keolahragaan — ITERA 2026")
    print(garis(48))
    print("  [1] 📋  Registrasi Atlet Baru")
    print("  [2] 🏃  Input Data Latihan")
    print("  [3] ❤   Evaluasi Kondisi Fisik")
    print("  [4] 📊  Analisis Progres Mingguan")
    print("  [5] 🔍  Cari Data Atlet")
    print("  [6] 💾  Simpan Data ke File")
    print("  [7] 📂  Muat Data dari File")
    print("  [8] 📄  Ekspor Laporan Ringkasan")
    print("  [0] 🚪  Keluar")
    print(garis(48))


def main():
    """Fungsi utama – loop menu aplikasi."""
    inisialisasi_file()

    while True:
        tampilkan_menu()
        pilihan = input("  Pilih menu [0-8]: ").strip()

        if pilihan == "1":
            registrasi_atlet()
        elif pilihan == "2":
            input_data_latihan()
        elif pilihan == "3":
            evaluasi_kondisi()
        elif pilihan == "4":
            analisis_progres()
        elif pilihan == "5":
            cari_data_atlet()
        elif pilihan == "6":
            simpan_data()
        elif pilihan == "7":
            muat_data()
        elif pilihan == "8":
            ekspor_laporan()
        elif pilihan == "0":
            print("\n  👋 Terima kasih! Sampai jumpa.")
            print(garis())
            break
        else:
            print("  ⚠  Pilihan tidak valid. Masukkan angka 0-8.")

if __name__ == "__main__":
    main()
