# ✨ VirtualFit AI — Next-Gen AI Virtual Try-On Platform

[![Laravel](https://img.shields.io/badge/Laravel-11.x-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)](https://laravel.com)
[![PHP](https://img.shields.io/badge/PHP-8.2+-777BB4?style=for-the-badge&logo=php&logoColor=white)](https://php.net)
[![TailwindCSS](https://img.shields.io/badge/TailwindCSS-3.x-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)](https://tailwindcss.com)
[![Alpine.js](https://img.shields.io/badge/Alpine.js-3.x-8BC0D0?style=for-the-badge&logo=alpinedotjs&logoColor=white)](https://alpinejs.dev)
[![LEFFA](https://img.shields.io/badge/LEFFA-CVPR_2025-blue?style=for-the-badge&logo=pytorch&logoColor=white)](https://github.com/franciszzj/Leffa)
[![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)](LICENSE)

**VirtualFit AI** is a complete, production-grade Virtual Try-On web platform built with **PHP Laravel 11**, **Tailwind CSS**, and **Alpine.js**, powered by the official **LEFFA (Learning Flow Fields in Attention - CVPR 2025)** deep learning diffusion pipeline running on free NVIDIA T4 GPU instances (Google Colab / Dedicated GPU Server).

---

## 🌟 Key Features

- 👤 **Interactive Model & Garment Selection**: Choose from curated professional model presets or upload your custom person photo and clothing images.
- 🏷️ **Smart Category Mapping**: Full support for `tops` (upper-body), `bottoms` (lower-body), and `one-pieces` (dresses/overalls) automatically mapped to neural masks.
- 📦 **Zero-Tunnel ZIP Workflow**:
  1. **Studio Export**: Bundles `person.png`, `garment.png`, and structured `metadata.json` into an exportable ZIP package.
  2. **Colab GPU Inference**: High-speed diffusion processing (~15–18s on NVIDIA T4 GPU with attention slicing and FP16 acceleration).
  3. **Seamless Import**: Upload the result ZIP directly back into Laravel with automatic permanent disk storage and database synchronisation.
- 🎚️ **Interactive Before/After Comparison Slider**: Smooth 0–100% split-view comparison slider supporting touch gestures and mouse dragging.
- 📊 **User Dashboard & Generation History**:
  - Filter by category (`all`, `tops`, `bottoms`, `one-pieces`).
  - View real-time platform statistics.
  - Delete past generations with automatic physical storage cleanup.
- 🛡️ **Built-in Security**:
  - Archive entry sanitisation and Path Traversal defense.
  - Image fallback rendering (zero broken image links).
  - Anti-crash Colab pipeline with PyTorch 2.6 `weights_only=False` support and memory-efficient attention slicing.

---

## 🏗️ Architecture & Workflow

```text
[ Laravel Web App (Local/Server) ]
       │
       ▼ (1) Export virtualfit_input_<id>.zip
[ User Downloads Input ZIP ]
       │
       ▼ (2) Upload to Google Colab GPU Notebook (free Tesla T4)
[ Official LEFFA Neural Diffusion (~15s) ]
       │
       ▼ (3) Download virtualfit_result_<id>.zip
[ Laravel Web App (Local/Server) ]
       │
       ▼ (4) Import ZIP -> Permanent Storage + DB -> Interactive Comparison Slider!
```

---

## 🚀 Getting Started

### Prerequisites
- **PHP** >= 8.2 with `fileinfo`, `gd`, and `zip` extensions enabled.
- **Composer** >= 2.0
- **MySQL** >= 8.0 or MariaDB >= 10.4
- **Node.js** >= 18.x & NPM (optional, for asset compiling)

### Installation Steps

1. **Clone the repository:**
   ```bash
   git clone https://github.com/<your-username>/virtualfit-ai.git
   cd virtualfit-ai
   ```

2. **Install PHP dependencies:**
   ```bash
   composer install
   ```

3. **Configure Environment:**
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```
   Update database credentials in `.env`:
   ```ini
   DB_CONNECTION=mysql
   DB_HOST=127.0.0.1
   DB_PORT=3306
   DB_DATABASE=virtualfit
   DB_USERNAME=root
   DB_PASSWORD=
   ```

4. **Run Database Migrations:**
   ```bash
   php artisan migrate
   ```

5. **Create Storage Symlink:**
   ```bash
   php artisan storage:link
   ```

6. **Start the Laravel Development Server:**
   ```bash
   php artisan serve --port=8000
   ```
   Open **[http://127.0.0.1:8000](http://127.0.0.1:8000)** in your browser!

---

## ⚡ Google Colab GPU Inference

To run the official LEFFA Virtual Try-On diffusion model for free on an NVIDIA T4 GPU:

1. Open [Google Colab](https://colab.research.google.com).
2. Upload the included notebook: **`virtualfit_leffa_colab.ipynb`** (or `LEFFA_COLAB_GPU_SERVER.ipynb`).
3. Set runtime to GPU: **Runtime** → **Change runtime type** → **T4 GPU**.
4. Run Steps 1 through 4 to initialize the environment and load weights.
5. In **Step 7**: Upload your `virtualfit_input_xxxx.zip` (or drag into Colab's left Files sidebar).
6. Click **Run** on Step 7 to generate your outfit in ~15 seconds and download `virtualfit_result_xxxx.zip`.
7. Import the ZIP back into Laravel at **`/studio`** or **`/dashboard`**!

---

## 📂 Project Structure

```text
├── app/
│   ├── Http/Controllers/
│   │   └── TryOnController.php       # Export ZIP, Import ZIP, Dashboard, History, Show
│   └── Models/
│       ├── TryOnGeneration.php       # Try-On generation record with URL accessors
│       ├── Person.php                # Model preset management
│       └── Garment.php               # Clothing preset management
├── database/
│   └── migrations/
│       └── 2026_11_23_093000_create_try_on_generations_table.php
├── resources/
│   └── views/tryon/
│       ├── layout.blade.php          # Global layout & ZIP import modal
│       ├── studio.blade.php          # Model & Garment selection studio
│       ├── dashboard.blade.php       # Stats & recent generations
│       ├── history.blade.php         # Filterable generations archive
│       └── result.blade.php          # Before/After interactive comparison slider
├── routes/
│   └── web.php                       # Application routes & aliases
├── virtualfit_leffa_colab.ipynb      # Official Colab GPU inference notebook
└── LEFFA_COLAB_GPU_SERVER.ipynb      # Alternative GPU server notebook
```

---

## 🔒 Security

- Archive inspection protects against Path Traversal attacks (`..`, absolute paths, null bytes).
- All files are verified and stored safely on private server disks with public storage symlinks.
- Temporary files and ZIP archives are automatically cleaned up after streaming downloads.

---

## 📄 License

The VirtualFit AI platform is open-source software licensed under the [MIT license](LICENSE).
LEFFA model checkpoints are subject to the original authors' [LEFFA License](https://github.com/franciszzj/Leffa).
