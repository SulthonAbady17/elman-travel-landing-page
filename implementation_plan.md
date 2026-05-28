# Rencana Implementasi: Formulir Kualifikasi Prospek Multi-Tahap (Section-Based)

Kami akan mengimplementasikan formulir interaktif multi-tahap (kuesioner kualifikasi) sebagai **Section Khusus** di dalam halaman [index.html](file:///d:/code/front-end/elman-gemilang-travel/index.html) (diletakkan tepat sebelum FAQ) menggunakan **Alpine.js**.

## Fitur Utama

1. **Section Khusus (`#konsultasi`)**:
   - Diletakkan di atas section FAQ.
   - Dibuat dengan visual card premium yang berfokus penuh agar pengguna tidak terganggu elemen lain saat mengisi.
2. **Navigasi CTA dengan Smooth Scroll**:
   - Semua tombol CTA utama (di Hero, Program, Footer) akan diubah untuk mengarahkan pengguna secara halus ke `#konsultasi`.
   - Menggunakan fungsi smooth scroll JavaScript atau utility HTML anchor.
3. **Optimasi Pelacakan Meta Pixel**:
   - **Tombol CTA Awal**: Mengirimkan event kustom `InitiateConsultation` (opsional) untuk mencatat ketertarikan awal.
   - **Tombol WhatsApp Akhir**: Hanya akan mengirimkan event standar **`Lead`** (`fbq('track', 'Lead')`) ketika formulir selesai diisi dan tombol "Konsultasi via WhatsApp" diklik. Ini memastikan Meta hanya mempelajari profil pengguna berkualitas tinggi (*high-intent leads*).
4. **Pertanyaan yang Diajukan (6 Langkah)**:
   - **Step 1**: Nama Lengkap (Input teks).
   - **Step 2**: Rencana Keberangkatan (Pilihan kartu: `< 1 bulan`, `1-3 bulan`, `3-6 bulan`, `Masih survey`).
   - **Step 3**: Jumlah Peserta (Pilihan kartu: `Sendiri`, `2 orang`, `3-5 orang`, `Rombongan/Keluarga`).
   - **Step 4**: Kota Asal (Input teks).
   - **Step 5**: Range Budget (Pilihan kartu: `20-25 Juta`, `25-30 Juta`, `30-40 Juta`, `Prioritas Fasilitas Terbaik`).
   - **Step 6**: Prioritas Utama (Pilihan kartu: `Hotel Dekat`, `Jadwal Nyaman`, `Pendamping Ibadah`, `Harga`, `Keamanan & Legalitas`).
5. **Halaman Terima Kasih & Kirim WA**:
   - Ringkasan singkat data yang diisi.
   - Tombol utama untuk mengirimkan pesan WhatsApp dengan format baris baru (`%0A`) yang rapi.

---

## Rencana Perubahan Kode

### 1. Struktur State Alpine.js di Section Form
Kita akan membuat section baru dengan `x-data` mandiri untuk menampung seluruh logika formulir agar kode tetap rapi dan modular:

```html
<section id="konsultasi" class="py-16 md:py-24 bg-gray-50 border-t border-gray-200">
  <div x-data="{
    formStep: 1,
    formData: {
      nama: '',
      keberangkatan: '',
      jumlahOrang: '',
      kota: '',
      budget: '',
      prioritas: ''
    },
    nextStep() {
      if (this.formStep < 7) this.formStep++;
    },
    prevStep() {
      if (this.formStep > 1) this.formStep--;
    },
    selectOption(field, value) {
      this.formData[field] = value;
      this.nextStep();
    },
    submitToWhatsApp() {
      // Kirim event Lead ke Meta Pixel
      if (typeof fbq === 'function') {
        fbq('track', 'Lead');
      }
      
      const teksPesan = `Assalamualaikum Wr. Wb.
Saya ingin berkonsultasi mengenai Program Umroh 2026.

Berikut detail kebutuhan saya:
• Nama Lengkap: ${this.formData.nama || '-'}
• Rencana Keberangkatan: ${this.formData.keberangkatan || '-'}
• Jumlah Peserta: ${this.formData.jumlahOrang || '-'}
• Kota Asal: ${this.formData.kota || '-'}
• Budget per Orang: ${this.formData.budget || '-'}
• Prioritas Utama: ${this.formData.prioritas || '-'}

Mohon rekomendasi program yang paling sesuai. Terima kasih.`;
      
      const phone = siteData.kontak ? siteData.kontak.whatsapp : '628123456789';
      const url = 'https://wa.me/' + phone + '?text=' + encodeURIComponent(teksPesan);
      window.open(url, '_blank');
    }
  }">
    <!-- Form Steps UI -->
  </div>
</section>
```

### 2. Modifikasi Link Tombol CTA di Landing Page
Mengubah href dari tombol-tombol agar langsung mengarah ke ID section `#konsultasi` dengan lancar:
- **Hero & Stats Bar CTA**: Mengubah href menjadi `#konsultasi` dan menambahkan pelacakan event klik awal jika diperlukan.
- **Program & Footer CTA**: Mengubah href menjadi `#konsultasi`.

---

## Rencana Verifikasi

### Manual Verification
1. Membuka website secara lokal.
2. Mengklik tombol CTA di bagian atas/tengah halaman dan memastikan halaman scroll dengan halus ke section `#konsultasi`.
3. Mengisi nama lengkap pada Step 1, lalu klik "Selanjutnya".
4. Menjawab seluruh pertanyaan pilihan kartu dan memastikan form berpindah ke step berikutnya secara otomatis setelah kartu diklik.
5. Pada langkah akhir (Step 7/Terima Kasih), klik tombol "Konsultasi via WhatsApp".
6. Memastikan tab baru terbuka dengan URL WhatsApp tujuan serta pesan terformat secara rapi (menggunakan baris baru/enter).
