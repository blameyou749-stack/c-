# c-
c++ and rust
cat << 'EOF' > src/main.rs
use std::ffi::CString;
use std::os::raw::c_char;

// Hubungkan fungsi penyaring dari file C++
extern "C" {
    fn saring_teks_crypto(teks: *const c_char) -> i32;
}

fn cek_pake_cpp(teks_rust: &str) -> bool {
    let teks_c = CString::new(teks_rust).unwrap();
    unsafe {
        saring_teks_crypto(teks_c.as_ptr()) == 1
    }
}

#[tokio::main]
async fn main() {
    println!("=== 🤖 BOT INTELIJEN CRYPTO MATA-MATA AKTIF ===");
    
    let url_target = "https://cointelegraph.com/rss";
    let client = reqwest::Client::builder()
        .user_agent("Mozilla/5.0")
        .build()
        .unwrap();

    println!("⚡ Menembak data live dari internet...");
    
    match client.get(url_target).send().await {
        Ok(respon) => {
            let teks_berita = respon.text().await.unwrap();
            println!("✅ Sukses mengunduh {} karakter data berita!", teks_berita.len());
            println!("🧠 Mengirim ke mesin penyaring C++ untuk mendeteksi koin bawah...\n");
            
            let mut info_ditemukan = false;

            for baris in teks_berita.lines() {
                if baris.contains("<title>") || baris.contains("<description>") {
                    // JINAKKAN BORROW CHECKER: Simpan jadi String permanen dulu
                    let teks_proses = baris
                        .replace("<title>", "")
                        .replace("</title>", "")
                        .replace("<description>", "")
                        .replace("</description>", "");
                    
                    let teks_bersih = teks_proses.trim();

                    // Oper ke mesin C++ 
                    if !teks_bersih.is_empty() && cek_pake_cpp(teks_bersih) {
                        println!("🚨 [C++ MENDETEKSI SINYAL KEBOCORAN INFO!]");
                        println!("👉 DATA: {}\n", teks_bersih);
                        info_ditemukan = true;
                    }
                }
            }

            if !info_ditemukan {
                println!("🗑️ [C++ Scan Kelar] Semua data diperiksa. Isinya cuma berita koin gede (Bitcoin/Ethereum/Sampah), otomatis dibuang.");
            }
        }
        Err(e) => {
            println!("❌ GAGAL TOTAL KONEKSI: {}", e);
        }
    }
    println!("\n=== 🏁 Selesai Memantau ===");
}
EOF
