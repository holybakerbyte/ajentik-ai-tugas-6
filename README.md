# ajentik-ai-tugas-6
A simple repo for tugas Agentic Ai "Kelas Otomesyen mentored by Aria Suseno.S.T.Avi"

Repository ini berisi konfigurasi workflow **n8n** untuk membangun AI Agent cerdas bernama **Gita**. Agent ini berfungsi untuk membalas pesan WhatsApp secara otomatis menggunakan model LLM dari OpenRouter, berbasis memori percakapan, serta memanfaatkan Google Docs sebagai basis pengetahuan (*Ground Truth / Knowledge Base*). Integration WhatsApp dilakukan menggunakan layanan pihak ketiga dari **Fonnte**.

## Fitur Utama

* **Integrasi WhatsApp Otomatis**: Menerima pesan masuk via Webhook dan mengirimkan balasan langsung via Fonnte API.
* **AI Agent (Gita)**: Menggunakan model pintar yang dikonfigurasi untuk menjawab secara ringkas, akurat, dan langsung ke inti masalah (Low Temperature 0.3).
* **Dynamic Language Matching**: Otomatis membalas menggunakan bahasa yang sama dengan yang digunakan oleh pengirim pesan.
* **RAG (Retrieval-Augmented Generation)**: Terintegrasi langsung dengan **Google Docs** ("compro in Google Docs") untuk membaca data profil perusahaan atau informasi produk sebagai acuan validasi jawaban.
* **Contextual Memory**: Dilengkapi dengan *Simple Memory Buffer Window* berbasis `sender ID` unik, sehingga AI tetap mengingat konteks percakapan sebelumnya.

---

## Arsitektur & Struktur Workflow

Workflow ini dibangun di atas n8n dengan susunan node sebagai berikut:

1. **Webhook Node**: Menerima data payload pesan masuk dari Fonnte (menangkap teks pesan dan nomor pengirim).
2. **AI Agent (LangChain)**: Otak utama sistem yang mengolah instruksi sistem (*System Message*) dan mengoordinasikan sub-node lainnya.
3. **OpenRouter Chat Model**: Menggunakan model `z-ai/glm-4.5-air:free` via OpenRouter sebagai mesin pemikir AI.
4. **Simple Memory**: Menyimpan memori sesi percakapan berdasarkan parameter ID pengirim (`$json.body.senderlid`).
5. **Google Docs Tool**: Berfungsi sebagai *tools* yang wajib diakses oleh AI Agent untuk mengambil dokumen acuan (Company Profile).
6. **HTTP Request Node**: Mengirimkan hasil output teks final dari AI Agent kembali ke API Fonnte via metode `POST` ke `https://api.fonnte.com/send`.

---

## Prasyarat & Kebutuhan Dokumen

Sebelum melakukan import workflow, pastikan Anda telah menyiapkan beberapa hal berikut:

1. **N8N Instance**: Akun atau self-hosted n8n terinstall (versi pendukung node LangChain).
2. **Akun Fonnte**: Token API Fonnte untuk autentikasi pengiriman pesan.
3. **Akun OpenRouter**: API Key dari OpenRouter untuk mengakses model `glm-4.5-air`.
4. **Google Cloud Console**: Kredensial OAuth2 untuk memberikan akses n8n membaca file Google Docs Anda.

---

## Cara Instalasi & Konfigurasi

1. **Salin Kode JSON**: Buka file `WH-fonnte-WA-semi-finale.json` yang ada di dalam repository ini, lalu salin seluruh kodenya.
2. **Import ke n8n**:
* Buka dashboard n8n Anda.
* Buat alur kerja baru (*Create new workflow*).
* Klik menu titik tiga di pojok kanan atas, lalu pilih **Import from JSON** (atau langsung paste menggunakan `Ctrl+V` / `Cmd+V` di area kerja).


3. **Konfigurasi Kredensial**:
* **OpenRouter Chat Model**: Masukkan API Key OpenRouter Anda pada bagian credentials.
* **Google Docs Tool**: Hubungkan dengan akun Google Anda yang memiliki akses baca ke link Google Docs yang tertera di parameter node.
* **HTTP Request (Fonnte)**: Pada bagian *Headers*, ganti nilai `Authorization` dengan Token Fonnte milik Anda sendiri.


4. **Konfigurasi Webhook Fonnte**:
* Salin URL Webhook *Production* atau *Test* dari node Webhook n8n Anda.
* Tempelkan URL tersebut ke pengaturan Webhook di dashboard akun Fonnte Anda agar setiap ada pesan masuk, data langsung diteruskan ke n8n.


5. **Aktifkan Workflow**: Klik tombol **Active** di pojok kanan atas n8n.

---

## Instruksi Sistem Agent (System Message)

Secara bawaan, Agent **Gita** dikonfigurasi dengan instruksi ketat sebagai berikut:

* Wajib berkonsultasi dengan tool Google Docs sebelum menjawab sebagai kebenaran utama.
* Bahasa balasan harus 100% sama dengan bahasa user.
* Gaya bicara singkat, padat, jelas, tanpa basa-basi, mengutamakan struktur poin-poin (*bullet points*).

---

## Lisensi

Proyek ini bebas digunakan dan dimodifikasi untuk kebutuhan personal maupun komersial.

## Selfnote
Cari tau koneksi ke memory yang lebih kompleks
