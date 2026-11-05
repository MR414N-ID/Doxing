#!/usr/bin/env python3
# lang: Python 3.8+, file: doxing.py, target: Termux Android
# install: pip install requests beautifulsoup4 colorama cloudscraper

import sys
import os
import json
import time
import datetime
import requests
import cloudscraper
from bs4 import BeautifulSoup
from colorama import init, Fore, Back, Style

init(autoreset=True)

# ─────────────────────────────────────────────
#  BANNER
# ─────────────────────────────────────────────

BEBEK = r"""
    _
___( )___
|  ( )  |
|_______|
  |   |
  |___|
 /|   |\
/ |___| \
"""

RAINBOW = [Fore.RED, Fore.YELLOW, Fore.GREEN, Fore.CYAN, Fore.BLUE, Fore.MAGENTA]

def rainbow_text(text):
    out = ""
    i = 0
    for ch in text:
        if ch != ' ':
            out += RAINBOW[i % len(RAINBOW)] + Style.BRIGHT + ch
            i += 1
        else:
            out += ch
    return out + Style.RESET_ALL

def print_divider():
    print(Fore.WHITE + Style.DIM + "─" * 60 + Style.RESET_ALL)

def print_banner():
    os.system("clear")
    print(Fore.YELLOW + Style.BRIGHT + BEBEK)
    print(rainbow_text("MR414N-ID"))
    print()
    print_divider()
    print(Fore.CYAN + Style.BRIGHT + "doxing" + Fore.WHITE + " | " +
          Fore.LIGHTWHITE_EX + "Tools pencarian data publik Indonesia dari 50+ sumber")
    print(Fore.LIGHTBLACK_EX + "       Scrape bansos, pendidikan, ASN, hukum, kesehatan, pengadaan & lebih")
    print_divider()
    print()

# ─────────────────────────────────────────────
#  INPUT HELPER
# ─────────────────────────────────────────────

def ask(prompt, optional=False):
    suffix = Fore.LIGHTBLACK_EX + " (skip=enter)" + Style.RESET_ALL if optional else ""
    val = input(Fore.YELLOW + "  » " + Fore.WHITE + prompt + suffix + Fore.GREEN + ": " + Style.RESET_ALL).strip()
    return val if val else None

def collect_inputs():
    print(Fore.CYAN + Style.BRIGHT + "\n  [ INPUT DATA ]\n")
    data = {}
    data['nik']       = ask("NIK (16 digit)", optional=True)
    data['nama']      = ask("Nama lengkap", optional=True)
    data['ttl']       = ask("Tanggal lahir (YYYY-MM-DD)", optional=True)
    data['provinsi']  = ask("Provinsi", optional=True)
    data['kabupaten'] = ask("Kabupaten/Kota", optional=True)
    data['kecamatan'] = ask("Kecamatan", optional=True)
    data['nisn']      = ask("NISN (siswa)", optional=True)
    data['nuptk']     = ask("NUPTK (guru)", optional=True)
    data['nidn']      = ask("NIDN (dosen)", optional=True)
    data['nip']       = ask("NIP (ASN)", optional=True)
    data['npwp']      = ask("NPWP", optional=True)
    data['nib']       = ask("NIB (izin usaha)", optional=True)
    data['no_perkara']= ask("Nomor perkara pengadilan", optional=True)
    data['str']       = ask("No STR (tenaga kesehatan)", optional=True)
    print()
    return data

# ─────────────────────────────────────────────
#  MENU
# ─────────────────────────────────────────────

MENU = [
    ( 1, "Bansos Kemensos (PKH/BPNT/BLT)"),
    ( 2, "PIP Kemdikbud (Beasiswa)"),
    ( 3, "NISN Data Siswa"),
    ( 4, "Info GTK (Guru)"),
    ( 5, "PDDIKTI (Mahasiswa/Dosen)"),
    ( 6, "Tracer Studi Dikti"),
    ( 7, "Simlitabmas (Penelitian Dosen)"),
    ( 8, "BPJS Ketenagakerjaan"),
    ( 9, "SIPP MenPAN (ASN)"),
    (10, "KPU DPT Pemilih"),
    (11, "AHU (Badan Hukum/Notaris)"),
    (12, "SILK Kemenkumham (Advokat)"),
    (13, "LPSE LKPP (Vendor Pengadaan)"),
    (14, "OSS (Izin Usaha/NIB)"),
    (15, "Pajak (NPWP Lookup)"),
    (16, "Satu Data Indonesia"),
    (17, "BPS Statistik Kependudukan"),
    (18, "BKKBN Program KB"),
    (19, "SID Kemendesa (Data Desa)"),
    (20, "SIMTUN BNPB (Bantuan Bencana)"),
    (21, "Direktori Madrasah"),
    (22, "EMIS Kemenag (Santri/Madrasah)"),
    (23, "SIAGA BNPB (Kebencanaan)"),
    (24, "Pikobar Jabar"),
    (25, "Open Data Bandung"),
    (26, "Data Jakarta"),
    (27, "Open Data Jateng"),
    (28, "PPID Kominfo"),
    (29, "SPPT PBB Pajak"),
    (30, "SIMPADDU Kemendagri"),
    (31, "DTTOT (Terduga Teroris)"),
    (32, "PPATK (Transaksi Mencurigakan)"),
    (33, "Srikandi (Arsip ASN)"),
    (34, "JDIH (Produk Hukum)"),
    (35, "Pusdataru Jateng"),
    (36, "SIRUP LKPP (RUP Pengadaan)"),
    (37, "SPSE LKPP (E-Procurement)"),
    (38, "Gaji ASN Simulator"),
    (39, "BKN (Data ASN/CPNS)"),
    (40, "SSCASN BKN (Pendaftar CPNS)"),
    (41, "SISTER Kemdikbud (Dosen)"),
    (42, "GTK Kemdikbud (Guru & Tendik)"),
    (43, "Kominfo SDM TIK"),
    (44, "BPOM (Registrasi Produk/Apoteker)"),
    (45, "KTKI Kemkes (Tenaga Kesehatan)"),
    (46, "Registrasi Konsil Kedokteran"),
    (47, "SIRANAP Kemkes (Ketersediaan RS)"),
    (48, "SIPP Mahkamah Agung (Perkara)"),
    (49, "Putusan MA"),
    (50, "Badilum MA (Perkara Pidana/Perdata)"),
    (51, "[ JALANKAN SEMUA ]"),
]

def print_menu():
    print(Fore.CYAN + Style.BRIGHT + "  [ MENU FITUR ]\n")
    for num, label in MENU:
        num_str = Fore.YELLOW + Style.BRIGHT + f"  [{num:>2}]" + Style.RESET_ALL
        if num == 51:
            print_divider()
            print(num_str + " " + Fore.GREEN + Style.BRIGHT + label)
        else:
            print(num_str + " " + Fore.WHITE + label)
    print()

# ─────────────────────────────────────────────
#  SESSION
# ─────────────────────────────────────────────

HEADERS = {
    'User-Agent': 'Mozilla/5.0 (Linux; Android 12; Pixel 6) '
                  'AppleWebKit/537.36 (KHTML, like Gecko) '
                  'Chrome/120.0.0.0 Mobile Safari/537.36',
    'Accept-Language': 'id-ID,id;q=0.9,en-US;q=0.8',
}

def get_session():
    s = cloudscraper.create_scraper()
    s.headers.update(HEADERS)
    return s

def safe_get(session, url, params=None, timeout=15):
    try:
        r = session.get(url, params=params, timeout=timeout)
        return r
    except Exception as e:
        return None

def safe_post(session, url, data=None, json_data=None, timeout=15):
    try:
        r = session.post(url, data=data, json=json_data, timeout=timeout)
        return r
    except Exception as e:
        return None

# ─────────────────────────────────────────────
#  HASIL TRACKER
# ─────────────────────────────────────────────

results = []

def print_result(label, data):
    print_divider()
    print(Fore.GREEN + Style.BRIGHT + f"  ✔ {label}")
    print_divider()
    if isinstance(data, dict):
        for k, v in data.items():
            print(Fore.YELLOW + f"    {k:<25}" + Fore.WHITE + f": {v}")
    elif isinstance(data, list):
        for item in data:
            if isinstance(item, dict):
                for k, v in item.items():
                    print(Fore.YELLOW + f"    {k:<25}" + Fore.WHITE + f": {v}")
                print()
            else:
                print(Fore.WHITE + f"    {item}")
    else:
        print(Fore.WHITE + f"    {data}")
    print()
    results.append({"sumber": label, "data": data})

def print_skip(label, reason="data tidak tersedia / input kurang"):
    print(Fore.LIGHTBLACK_EX + f"  ✘ {label}: {reason}")

def print_error(label, reason="gagal koneksi"):
    print(Fore.RED + f"  ✘ {label}: {reason}")

# ─────────────────────────────────────────────
#  CAPTCHA HANDLER
# ─────────────────────────────────────────────

def solve_captcha_manual(image_url=None, hint=""):
    print(Fore.MAGENTA + "\n  [CAPTCHA]" + Style.RESET_ALL)
    if image_url:
        print(Fore.WHITE + f"  Buka URL ini di browser: {image_url}")
    if hint:
        print(Fore.WHITE + f"  Petunjuk: {hint}")
    answer = input(Fore.YELLOW + "  Masukan jawaban captcha (atau enter untuk skip): " + Style.RESET_ALL).strip()
    return answer if answer else None

# ─────────────────────────────────────────────
#  DEKODERS NIK
# ─────────────────────────────────────────────

def decode_nik(nik):
    if not nik or len(nik) != 16:
        return {}
    prov   = nik[0:2]
    kab    = nik[2:4]
    kec    = nik[4:6]
    tgl    = int(nik[6:8])
    bln    = nik[8:10]
    thn    = nik[10:12]
    urut   = nik[12:16]
    gender = "Perempuan" if tgl > 40 else "Laki-laki"
    tgl_asli = tgl - 40 if tgl > 40 else tgl
    tahun_full = int("19" + thn) if int(thn) > 30 else int("20" + thn)
    return {
        "Kode Provinsi" : prov,
        "Kode Kab/Kota" : kab,
        "Kode Kecamatan": kec,
        "Tanggal Lahir" : f"{tgl_asli:02d}-{bln}-{tahun_full}",
        "Jenis Kelamin" : gender,
        "No Urut"       : urut,
    }

# ─────────────────────────────────────────────
#  SCRAPER FUNCTIONS
# ─────────────────────────────────────────────

def f01_bansos(d, s):
    label = "Bansos Kemensos (PKH/BPNT/BLT)"
    if not d['nama'] or not d['provinsi'] or not d['kabupaten']:
        print_skip(label, "butuh nama + provinsi + kabupaten"); return
    try:
        r = safe_get(s, "https://cekbansos.kemensos.go.id/")
        if not r:
            print_error(label); return
        soup = BeautifulSoup(r.text, 'html.parser')
        captcha_img = soup.find('img', {'id': 'captcha_image'})
        cap_url = None
        if captcha_img:
            cap_url = "https://cekbansos.kemensos.go.id/" + captcha_img.get('src','')
        captcha = solve_captcha_manual(cap_url, "Ketik teks dalam gambar captcha")
        if not captcha:
            print_skip(label, "captcha dilewati"); return
        token = ""
        inp = soup.find('input', {'name': '_token'})
        if inp:
            token = inp.get('value', '')
        payload = {
            '_token'     : token,
            'prop'       : d['provinsi'].upper(),
            'kab'        : d['kabupaten'].upper(),
            'kec'        : d.get('kecamatan', ''),
            'kel'        : '',
            'nama_penerima': d['nama'].upper(),
            'captcha'    : captcha,
        }
        r2 = safe_post(s, "https://cekbansos.kemensos.go.id/", data=payload)
        if not r2:
            print_error(label); return
        soup2 = BeautifulSoup(r2.text, 'html.parser')
        rows = soup2.select('table tbody tr')
        if not rows:
            print_skip(label, "tidak ditemukan dalam database bansos"); return
        data_list = []
        for row in rows[:10]:
            cols = [c.get_text(strip=True) for c in row.find_all('td')]
            if cols:
                data_list.append({"Nama": cols[0] if len(cols)>0 else "-",
                                   "Wilayah": cols[1] if len(cols)>1 else "-",
                                   "Jenis Bansos": cols[2] if len(cols)>2 else "-",
                                   "Periode": cols[3] if len(cols)>3 else "-"})
        if data_list:
            print_result(label, data_list)
        else:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f02_pip(d, s):
    label = "PIP Kemdikbud (Beasiswa)"
    if not d['nik'] and not d['nisn']:
        print_skip(label, "butuh NIK atau NISN"); return
    try:
        url = "https://pip.kemdikbud.go.id/home/siswa"
        payload = {'nik': d['nik'] or '', 'nisn': d['nisn'] or ''}
        r = safe_post(s, url, data=payload)
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            soup = BeautifulSoup(r.text, 'html.parser')
            tbl = soup.find('table')
            if tbl:
                rows = tbl.find_all('tr')
                data_list = []
                for row in rows[1:6]:
                    cols = [c.get_text(strip=True) for c in row.find_all('td')]
                    if cols:
                        data_list.append({"data": " | ".join(cols)})
                print_result(label, data_list) if data_list else print_skip(label, "tidak ditemukan")
            else:
                print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f03_nisn(d, s):
    label = "NISN Data Siswa"
    if not d['nama'] and not d['nisn']:
        print_skip(label, "butuh nama atau NISN"); return
    try:
        if d['nisn']:
            url = f"https://nisn.data.kemdikbud.go.id/index.php/Cjl/prosesNisn"
            payload = {'nisn': d['nisn']}
        else:
            url = "https://nisn.data.kemdikbud.go.id/index.php/Cjl/prosesNama"
            payload = {'nama': d['nama'], 'tempat_lahir': '', 'tgl_lahir': d['ttl'] or ''}
        r = safe_post(s, url, data=payload)
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "format respons tidak dikenali")
    except Exception as e:
        print_error(label, str(e))

def f04_gtk(d, s):
    label = "Info GTK (Guru)"
    if not d['nuptk'] and not d['nama']:
        print_skip(label, "butuh NUPTK atau nama"); return
    try:
        url = "https://info.gtk.kemdikbud.go.id/api/gtk"
        params = {'nuptk': d['nuptk'] or '', 'nama': d['nama'] or ''}
        r = safe_get(s, url, params=params)
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f05_pddikti(d, s):
    label = "PDDIKTI (Mahasiswa/Dosen)"
    if not d['nama'] and not d['nidn']:
        print_skip(label, "butuh nama atau NIDN"); return
    try:
        q = d['nidn'] or d['nama']
        url = f"https://api-frontend.kemdikbud.go.id/v2/pencarian/mhs/{q}"
        r = safe_get(s, url)
        if not r:
            print_error(label); return
        try:
            js = r.json()
            mahasiswa = js.get('mahasiswa', [])[:5]
            if mahasiswa:
                print_result(label, [{"Nama": m.get('nama',''), "PT": m.get('namapt',''),
                                       "Prodi": m.get('namaprodi',''), "Status": m.get('statusmahasiswa','')}
                                      for m in mahasiswa])
            else:
                url2 = f"https://api-frontend.kemdikbud.go.id/v2/pencarian/dosen/{q}"
                r2 = safe_get(s, url2)
                if r2:
                    js2 = r2.json()
                    dosen = js2.get('dosen', [])[:5]
                    if dosen:
                        print_result(label, [{"Nama": m.get('nama',''), "PT": m.get('namapt',''),
                                               "NIDN": m.get('nidn','')} for m in dosen])
                    else:
                        print_skip(label, "tidak ditemukan")
                else:
                    print_skip(label, "tidak ditemukan")
        except:
            print_skip(label, "format respons tidak dikenali")
    except Exception as e:
        print_error(label, str(e))

def f06_tracer(d, s):
    label = "Tracer Studi Dikti"
    if not d['nama']:
        print_skip(label, "butuh nama"); return
    try:
        url = "https://tracerstudi.kemdikbud.go.id/api/search"
        r = safe_get(s, url, params={'nama': d['nama']})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f07_simlitabmas(d, s):
    label = "Simlitabmas (Penelitian Dosen)"
    if not d['nama'] and not d['nidn']:
        print_skip(label, "butuh nama atau NIDN"); return
    try:
        url = "https://simlitabmas.kemdikbud.go.id/eselon3/dosen/pencarian"
        r = safe_get(s, url, params={'nidn': d['nidn'] or '', 'nama': d['nama'] or ''})
        if not r:
            print_error(label); return
        soup = BeautifulSoup(r.text, 'html.parser')
        rows = soup.select('table tbody tr')
        if rows:
            data_list = [{"data": " | ".join(c.get_text(strip=True) for c in row.find_all('td'))}
                         for row in rows[:5]]
            print_result(label, data_list)
        else:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f08_bpjstk(d, s):
    label = "BPJS Ketenagakerjaan"
    if not d['nik']:
        print_skip(label, "butuh NIK"); return
    try:
        url = "https://sso.bpjsketenagakerjaan.go.id/cas/v1/tickets"
        r = safe_get(s, f"https://www.bpjsketenagakerjaan.go.id/cek-status.html?nik={d['nik']}")
        if not r:
            print_error(label); return
        soup = BeautifulSoup(r.text, 'html.parser')
        result_div = soup.find('div', {'class': 'result'}) or soup.find('table')
        if result_div:
            print_result(label, {"raw": result_div.get_text(separator=' ', strip=True)[:500]})
        else:
            print_skip(label, "endpoint memerlukan autentikasi penuh")
    except Exception as e:
        print_error(label, str(e))

def f09_sipp_menpan(d, s):
    label = "SIPP MenPAN (ASN)"
    if not d['nama'] and not d['nip']:
        print_skip(label, "butuh nama atau NIP"); return
    try:
        url = "https://sipp.menpan.go.id/api/pegawai/cari"
        r = safe_get(s, url, params={'nama': d['nama'] or '', 'nip': d['nip'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan / endpoint berubah")
    except Exception as e:
        print_error(label, str(e))

def f10_kpu_dpt(d, s):
    label = "KPU DPT Pemilih"
    if not d['nama'] or not d['provinsi']:
        print_skip(label, "butuh nama + provinsi"); return
    try:
        url = "https://cekdptonline.kpu.go.id/"
        if d['nik']:
            r = safe_post(s, url, data={'nik': d['nik']})
        else:
            r = safe_post(s, url, data={'nama': d['nama'], 'provinsi': d['provinsi']})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            soup = BeautifulSoup(r.text, 'html.parser')
            tbl = soup.find('table')
            if tbl:
                rows = tbl.find_all('tr')
                dl = [{"data": " | ".join(c.get_text(strip=True) for c in row.find_all('td'))} for row in rows[1:6]]
                print_result(label, dl) if dl else print_skip(label, "tidak ditemukan")
            else:
                print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f11_ahu(d, s):
    label = "AHU (Badan Hukum/Notaris)"
    if not d['nama']:
        print_skip(label, "butuh nama"); return
    try:
        url = f"https://ahu.go.id/api/perseroan/cari?nama={d['nama']}"
        r = safe_get(s, url)
        if not r:
            print_error(label); return
        try:
            js = r.json()
            items = js.get('data', js)
            if isinstance(items, list):
                print_result(label, [{"Nama": i.get('nama',''), "No SK": i.get('no_sk',''),
                                       "Status": i.get('status','')} for i in items[:5]])
            else:
                print_result(label, items)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f12_silk(d, s):
    label = "SILK Kemenkumham (Advokat)"
    if not d['nama']:
        print_skip(label, "butuh nama"); return
    try:
        url = "https://api.kemenkumham.go.id/silk/advokat/cari"
        r = safe_get(s, url, params={'nama': d['nama']})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan / endpoint berubah")
    except Exception as e:
        print_error(label, str(e))

def f13_lpse(d, s):
    label = "LPSE LKPP (Vendor Pengadaan)"
    if not d['nama'] and not d['npwp']:
        print_skip(label, "butuh nama atau NPWP"); return
    try:
        url = "https://inaproc.id/api/penyedia/cari"
        r = safe_get(s, url, params={'nama': d['nama'] or '', 'npwp': d['npwp'] or ''})
        if not r:
            url2 = f"https://lpse.lkpp.go.id/eproc4/rekanan/search?keyword={d['nama'] or d['npwp']}"
            r = safe_get(s, url2)
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            soup = BeautifulSoup(r.text, 'html.parser')
            tbl = soup.find('table')
            if tbl:
                rows = tbl.find_all('tr')
                dl = [{"data": " | ".join(c.get_text(strip=True) for c in row.find_all('td'))} for row in rows[1:6]]
                print_result(label, dl) if dl else print_skip(label, "tidak ditemukan")
            else:
                print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f14_oss(d, s):
    label = "OSS (Izin Usaha/NIB)"
    if not d['nib'] and not d['nama']:
        print_skip(label, "butuh NIB atau nama usaha"); return
    try:
        url = "https://oss.go.id/api/perizinan/cari"
        r = safe_get(s, url, params={'nib': d['nib'] or '', 'nama': d['nama'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan / endpoint berubah")
    except Exception as e:
        print_error(label, str(e))

def f15_pajak(d, s):
    label = "Pajak (NPWP Lookup)"
    if not d['npwp'] and not d['nama']:
        print_skip(label, "butuh NPWP atau nama"); return
    try:
        url = "https://ereg.pajak.go.id/cetak/npwp"
        r = safe_get(s, url, params={'npwp': d['npwp'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "perlu autentikasi DJP")
    except Exception as e:
        print_error(label, str(e))

def f16_satudata(d, s):
    label = "Satu Data Indonesia"
    try:
        url = "https://data.go.id/api/3/action/package_search"
        q = d['kabupaten'] or d['provinsi'] or "kependudukan"
        r = safe_get(s, url, params={'q': q, 'rows': 5})
        if not r:
            print_error(label); return
        js = r.json()
        results_list = js.get('result', {}).get('results', [])
        if results_list:
            print_result(label, [{"Judul": x.get('title',''), "Org": x.get('organization',{}).get('title','')}
                                  for x in results_list[:5]])
        else:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f17_bps(d, s):
    label = "BPS Statistik"
    try:
        url = "https://webapi.bps.go.id/v1/api/list/model/data/lang/ind/domain/0000/var/529/key/demo"
        r = safe_get(s, url)
        if not r:
            print_error(label); return
        print_result(label, {"info": "BPS API aktif — data statistik kependudukan tersedia",
                              "url": "https://webapi.bps.go.id"})
    except Exception as e:
        print_error(label, str(e))

def f18_bkkbn(d, s):
    label = "BKKBN Program KB"
    if not d['provinsi']:
        print_skip(label, "butuh provinsi"); return
    try:
        url = "https://aplikasi.bkkbn.go.id/api/peserta"
        r = safe_get(s, url, params={'provinsi': d['provinsi']})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "perlu autentikasi internal BKKBN")
    except Exception as e:
        print_error(label, str(e))

def f19_sid(d, s):
    label = "SID Kemendesa (Data Desa)"
    if not d['nama'] and not d['kabupaten']:
        print_skip(label, "butuh nama atau wilayah"); return
    try:
        url = "https://sid.kemendesa.go.id/api/warga/cari"
        r = safe_get(s, url, params={'nama': d['nama'] or '', 'kab': d['kabupaten'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "perlu akses desa terdaftar")
    except Exception as e:
        print_error(label, str(e))

def f20_simtun(d, s):
    label = "SIMTUN BNPB (Bantuan Bencana)"
    if not d['nama'] and not d['provinsi']:
        print_skip(label, "butuh nama atau provinsi"); return
    try:
        url = "https://simtun.bnpb.go.id/api/penerima"
        r = safe_get(s, url, params={'nama': d['nama'] or '', 'provinsi': d['provinsi'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan / perlu autentikasi")
    except Exception as e:
        print_error(label, str(e))

def f21_madrasah(d, s):
    label = "Direktori Madrasah"
    if not d['nama']:
        print_skip(label, "butuh nama"); return
    try:
        url = f"https://emis.kemenag.go.id/api/siswa/cari?nama={d['nama']}"
        r = safe_get(s, url)
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f22_emis(d, s):
    label = "EMIS Kemenag (Santri/Madrasah)"
    if not d['nama'] and not d['nik']:
        print_skip(label, "butuh nama atau NIK"); return
    try:
        url = "https://emis.kemenag.go.id/api/santri/cari"
        r = safe_get(s, url, params={'nama': d['nama'] or '', 'nik': d['nik'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan / perlu autentikasi")
    except Exception as e:
        print_error(label, str(e))

def f23_siaga(d, s):
    label = "SIAGA BNPB (Kebencanaan)"
    if not d['provinsi']:
        print_skip(label, "butuh provinsi"); return
    try:
        url = "https://bnpb.go.id/api/kejadian"
        r = safe_get(s, url, params={'provinsi': d['provinsi']})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f24_pikobar(d, s):
    label = "Pikobar Jabar"
    if not d['nik'] and not d['nama']:
        print_skip(label, "butuh NIK atau nama"); return
    try:
        url = "https://pikobar.jabarprov.go.id/api/warga/cari"
        r = safe_get(s, url, params={'nik': d['nik'] or '', 'nama': d['nama'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "perlu autentikasi Pikobar")
    except Exception as e:
        print_error(label, str(e))

def f25_opendata_bdg(d, s):
    label = "Open Data Bandung"
    try:
        url = "https://opendata.bandung.go.id/api/3/action/package_search"
        r = safe_get(s, url, params={'q': d['nama'] or 'kependudukan', 'rows': 5})
        if not r:
            print_error(label); return
        js = r.json()
        res = js.get('result', {}).get('results', [])
        if res:
            print_result(label, [{"Judul": x.get('title','')} for x in res[:5]])
        else:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f26_data_jkt(d, s):
    label = "Data Jakarta"
    try:
        url = "https://data.jakarta.go.id/api/3/action/package_search"
        r = safe_get(s, url, params={'q': d['nama'] or 'kependudukan', 'rows': 5})
        if not r:
            print_error(label); return
        js = r.json()
        res = js.get('result', {}).get('results', [])
        if res:
            print_result(label, [{"Judul": x.get('title','')} for x in res[:5]])
        else:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f27_jateng(d, s):
    label = "Open Data Jateng"
    try:
        url = "https://data.jatengprov.go.id/api/3/action/package_search"
        r = safe_get(s, url, params={'q': d['nama'] or 'kependudukan', 'rows': 5})
        if not r:
            print_error(label); return
        js = r.json()
        res = js.get('result', {}).get('results', [])
        if res:
            print_result(label, [{"Judul": x.get('title','')} for x in res[:5]])
        else:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f28_ppid(d, s):
    label = "PPID Kominfo"
    try:
        url = "https://ppid.kominfo.go.id/api/informasi"
        r = safe_get(s, url, params={'q': d['nama'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f29_sppt(d, s):
    label = "SPPT PBB"
    if not d['nik'] and not d['nama']:
        print_skip(label, "butuh NIK atau nama"); return
    try:
        url = "https://bprd.jakarta.go.id/sppt/cari"
        r = safe_get(s, url, params={'nik': d['nik'] or '', 'nama': d['nama'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "perlu autentikasi BPRD")
    except Exception as e:
        print_error(label, str(e))

def f30_simpaddu(d, s):
    label = "SIMPADDU Kemendagri"
    if not d['nik']:
        print_skip(label, "butuh NIK"); return
    try:
        url = "https://simpaddu.kemendagri.go.id/api/penduduk"
        r = safe_get(s, url, params={'nik': d['nik']})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "perlu MOU Dukcapil")
    except Exception as e:
        print_error(label, str(e))

def f31_dttot(d, s):
    label = "DTTOT (Daftar Terduga Teroris)"
    if not d['nama']:
        print_skip(label, "butuh nama"); return
    try:
        url = "https://dttot.go.id/search"
        r = safe_get(s, url, params={'nama': d['nama']})
        if not r:
            print_error(label); return
        soup = BeautifulSoup(r.text, 'html.parser')
        tbl = soup.find('table')
        if tbl:
            rows = tbl.find_all('tr')
            dl = [{"data": " | ".join(c.get_text(strip=True) for c in row.find_all('td'))} for row in rows[1:6]]
            print_result(label, dl) if dl else print_skip(label, "tidak ditemukan dalam daftar")
        else:
            print_skip(label, "tidak ditemukan dalam daftar")
    except Exception as e:
        print_error(label, str(e))

def f32_ppatk(d, s):
    label = "PPATK"
    print_skip(label, "data PPATK tidak tersedia publik — perlu MOU resmi")

def f33_srikandi(d, s):
    label = "Srikandi (Arsip ASN)"
    if not d['nip'] and not d['nama']:
        print_skip(label, "butuh NIP atau nama"); return
    try:
        url = "https://srikandi.go.id/api/arsip/cari"
        r = safe_get(s, url, params={'nip': d['nip'] or '', 'nama': d['nama'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "perlu autentikasi Srikandi")
    except Exception as e:
        print_error(label, str(e))

def f34_jdih(d, s):
    label = "JDIH (Produk Hukum)"
    if not d['nama'] and not d['provinsi']:
        print_skip(label, "butuh nama atau provinsi"); return
    try:
        url = "https://jdih.go.id/api/peraturan"
        r = safe_get(s, url, params={'q': d['nama'] or d['provinsi']})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f35_pusdataru(d, s):
    label = "Pusdataru Jateng"
    try:
        url = "https://pusdataru.jatengprov.go.id/api/data"
        r = safe_get(s, url, params={'wilayah': d['kabupaten'] or d['provinsi'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f36_sirup(d, s):
    label = "SIRUP LKPP (RUP Pengadaan)"
    if not d['nama'] and not d['provinsi']:
        print_skip(label, "butuh nama instansi atau provinsi"); return
    try:
        url = "https://sirup.lkpp.go.id/sirup/ro/rekapPaketApsatker"
        r = safe_get(s, url, params={'tahun': datetime.datetime.now().year,
                                      'kodeprovinsi': '', 'idKL': '',
                                      'namaSatker': d['nama'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f37_spse(d, s):
    label = "SPSE LKPP (E-Procurement)"
    if not d['nama'] and not d['npwp']:
        print_skip(label, "butuh nama vendor atau NPWP"); return
    try:
        url = f"https://lpse.lkpp.go.id/eproc4/rekanan/search?keyword={d['nama'] or d['npwp']}"
        r = safe_get(s, url)
        if not r:
            print_error(label); return
        soup = BeautifulSoup(r.text, 'html.parser')
        rows = soup.select('table tbody tr')
        if rows:
            dl = [{"data": " | ".join(c.get_text(strip=True) for c in row.find_all('td'))} for row in rows[:5]]
            print_result(label, dl)
        else:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f38_gaji_asn(d, s):
    label = "Gaji ASN Simulator"
    try:
        url = "https://gaji.go.id/api/simulasi"
        r = safe_get(s, url)
        if not r:
            print_error(label); return
        print_result(label, {"info": "Simulasi gaji ASN tersedia di https://gaji.go.id",
                              "catatan": "data berdasarkan golongan, bukan NIK"})
    except Exception as e:
        print_error(label, str(e))

def f39_bkn(d, s):
    label = "BKN (Data ASN/CPNS)"
    if not d['nik'] and not d['nama']:
        print_skip(label, "butuh NIK atau nama"); return
    try:
        url = "https://www.bkn.go.id/layanan/digital/cari-nip"
        r = safe_get(s, url, params={'nik': d['nik'] or '', 'nama': d['nama'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            soup = BeautifulSoup(r.text, 'html.parser')
            tbl = soup.find('table')
            if tbl:
                rows = tbl.find_all('tr')
                dl = [{"data": " | ".join(c.get_text(strip=True) for c in row.find_all('td'))} for row in rows[1:6]]
                print_result(label, dl) if dl else print_skip(label, "tidak ditemukan")
            else:
                print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f40_sscasn(d, s):
    label = "SSCASN BKN (Pendaftar CPNS)"
    if not d['nik']:
        print_skip(label, "butuh NIK"); return
    try:
        url = f"https://sscasn.bkn.go.id/api/peserta/cek-nik/{d['nik']}"
        r = safe_get(s, url)
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan / endpoint berubah")
    except Exception as e:
        print_error(label, str(e))

def f41_sister(d, s):
    label = "SISTER Kemdikbud (Dosen)"
    if not d['nidn'] and not d['nama']:
        print_skip(label, "butuh NIDN atau nama"); return
    try:
        url = "https://sister.kemdikbud.go.id/api/dosen/cari"
        r = safe_get(s, url, params={'nidn': d['nidn'] or '', 'nama': d['nama'] or ''})
        if not r:
            url2 = f"https://api-frontend.kemdikbud.go.id/v2/pencarian/dosen/{d['nidn'] or d['nama']}"
            r = safe_get(s, url2)
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f42_gtk2(d, s):
    label = "GTK Kemdikbud (Guru & Tendik)"
    if not d['nik'] and not d['nuptk']:
        print_skip(label, "butuh NIK atau NUPTK"); return
    try:
        url = "https://gtk.kemdikbud.go.id/api/guru/cari"
        r = safe_get(s, url, params={'nik': d['nik'] or '', 'nuptk': d['nuptk'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan / perlu autentikasi GTK")
    except Exception as e:
        print_error(label, str(e))

def f43_kominfo(d, s):
    label = "Kominfo SDM TIK"
    if not d['nama']:
        print_skip(label, "butuh nama"); return
    try:
        url = "https://sdmtik.kominfo.go.id/api/peserta/cari"
        r = safe_get(s, url, params={'nama': d['nama']})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f44_bpom(d, s):
    label = "BPOM (Registrasi Produk/Apoteker)"
    if not d['nama']:
        print_skip(label, "butuh nama"); return
    try:
        url = f"https://cekbpom.pom.go.id/api/produk/cari?nama={d['nama']}"
        r = safe_get(s, url)
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f45_ktki(d, s):
    label = "KTKI Kemkes (Tenaga Kesehatan)"
    if not d['str'] and not d['nama']:
        print_skip(label, "butuh No STR atau nama"); return
    try:
        url = "https://registrasi.ktki.kemkes.go.id/api/tenaga/cari"
        r = safe_get(s, url, params={'str': d['str'] or '', 'nama': d['nama'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f46_konsil(d, s):
    label = "Registrasi Konsil Kedokteran"
    if not d['str'] and not d['nama']:
        print_skip(label, "butuh No STR atau nama"); return
    try:
        url = "https://registrasikonsil.go.id/api/dokter/cari"
        r = safe_get(s, url, params={'str': d['str'] or '', 'nama': d['nama'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f47_siranap(d, s):
    label = "SIRANAP Kemkes (Ketersediaan RS)"
    if not d['provinsi']:
        print_skip(label, "butuh provinsi"); return
    try:
        url = "https://yankes.kemkes.go.id/app/siranap/api/rumah_sakit"
        r = safe_get(s, url, params={'wilayah': d['provinsi']})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            items = js if isinstance(js, list) else js.get('data', [])
            print_result(label, [{"Nama RS": x.get('nama',''), "Kota": x.get('kota',''),
                                   "Kapasitas IGD": x.get('igd','')} for x in items[:5]])
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f48_sipp_ma(d, s):
    label = "SIPP Mahkamah Agung (Perkara)"
    if not d['nama'] and not d['no_perkara']:
        print_skip(label, "butuh nama atau no perkara"); return
    try:
        url = "https://sipp.mahkamahagung.go.id/api/pencarian"
        r = safe_get(s, url, params={'nama': d['nama'] or '', 'noperkara': d['no_perkara'] or ''})
        if not r:
            print_error(label); return
        try:
            js = r.json()
            print_result(label, js)
        except:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f49_putusan_ma(d, s):
    label = "Putusan Mahkamah Agung"
    if not d['nama'] and not d['no_perkara']:
        print_skip(label, "butuh nama atau no perkara"); return
    try:
        url = "https://putusan3.mahkamahagung.go.id/search.html"
        r = safe_get(s, url, params={'q': d['nama'] or d['no_perkara']})
        if not r:
            print_error(label); return
        soup = BeautifulSoup(r.text, 'html.parser')
        items = soup.select('.putusanlist li')[:5]
        if items:
            print_result(label, [{"Putusan": i.get_text(strip=True)} for i in items])
        else:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

def f50_badilum(d, s):
    label = "Badilum MA (Perkara Pidana/Perdata)"
    if not d['nama'] and not d['no_perkara']:
        print_skip(label, "butuh nama atau no perkara"); return
    try:
        url = "https://badilum.mahkamahagung.go.id/pencarian"
        r = safe_get(s, url, params={'q': d['nama'] or d['no_perkara']})
        if not r:
            print_error(label); return
        soup = BeautifulSoup(r.text, 'html.parser')
        rows = soup.select('table tbody tr')
        if rows:
            dl = [{"data": " | ".join(c.get_text(strip=True) for c in row.find_all('td'))} for row in rows[:5]]
            print_result(label, dl)
        else:
            print_skip(label, "tidak ditemukan")
    except Exception as e:
        print_error(label, str(e))

# ─────────────────────────────────────────────
#  DISPATCH TABLE
# ─────────────────────────────────────────────

FUNCS = {
     1: f01_bansos,   2: f02_pip,       3: f03_nisn,
     4: f04_gtk,      5: f05_pddikti,   6: f06_tracer,
     7: f07_simlitabmas, 8: f08_bpjstk, 9: f09_sipp_menpan,
    10: f10_kpu_dpt,  11: f11_ahu,      12: f12_silk,
    13: f13_lpse,     14: f14_oss,      15: f15_pajak,
    16: f16_satudata, 17: f17_bps,      18: f18_bkkbn,
    19: f19_sid,      20: f20_simtun,   21: f21_madrasah,
    22: f22_emis,     23: f23_siaga,    24: f24_pikobar,
    25: f25_opendata_bdg, 26: f26_data_jkt, 27: f27_jateng,
    28: f28_ppid,     29: f29_sppt,     30: f30_simpaddu,
    31: f31_dttot,    32: f32_ppatk,    33: f33_srikandi,
    34: f34_jdih,     35: f35_pusdataru,36: f36_sirup,
    37: f37_spse,     38: f38_gaji_asn, 39: f39_bkn,
    40: f40_sscasn,   41: f41_sister,   42: f42_gtk2,
    43: f43_kominfo,  44: f44_bpom,     45: f45_ktki,
    46: f46_konsil,   47: f47_siranap,  48: f48_sipp_ma,
    49: f49_putusan_ma, 50: f50_badilum,
}

# ─────────────────────────────────────────────
#  SIMPAN
# ─────────────────────────────────────────────

def save_results(inp):
    print_divider()
    simpan = input(Fore.YELLOW + "  Simpan hasil? " + Fore.WHITE + "[Y/n]: " + Style.RESET_ALL).strip().lower()
    if simpan in ('', 'y', 'yes'):
        ts = datetime.datetime.now().strftime("%Y%m%d_%H%M%S")
        fname = f"hasil_doxing_{ts}.json"
        out = {
            "timestamp": ts,
            "input": {k: v for k, v in inp.items() if v},
            "hasil": results,
        }
        with open(fname, 'w', encoding='utf-8') as f:
            json.dump(out, f, ensure_ascii=False, indent=2)
        print(Fore.GREEN + Style.BRIGHT + f"\n  ✔ Tersimpan → {fname}")
    else:
        print(Fore.LIGHTBLACK_EX + "\n  tidak disimpan.")
    print_divider()

# ─────────────────────────────────────────────
#  MAIN
# ─────────────────────────────────────────────

def main():
    print_banner()

    # decode NIK dulu kalau ada
    inp = collect_inputs()

    if inp.get('nik'):
        decoded = decode_nik(inp['nik'])
        if decoded:
            print(Fore.CYAN + Style.BRIGHT + "  [ DECODE NIK ]\n")
            for k, v in decoded.items():
                print(Fore.YELLOW + f"    {k:<25}" + Fore.WHITE + f": {v}")
            print()
            # auto-fill TTL dan gender dari NIK kalau kosong
            if not inp['ttl']:
                tgl_bagian = decoded.get('Tanggal Lahir', '')
                if tgl_bagian:
                    parts = tgl_bagian.split('-')
                    if len(parts) == 3:
                        inp['ttl'] = f"{parts[2]}-{parts[1]}-{parts[0]:>02}"

    print_divider()
    print_menu()

    pilih = input(Fore.YELLOW + "  Pilih nomor fitur: " + Style.RESET_ALL).strip()

    try:
        nomor = int(pilih)
    except:
        print(Fore.RED + "  Input tidak valid."); return

    if nomor not in list(range(1, 52)):
        print(Fore.RED + "  Nomor tidak ada."); return

    s = get_session()
    print()
    print_divider()

    if nomor == 51:
        print(Fore.GREEN + Style.BRIGHT + "  Menjalankan semua 50 fitur...\n")
        for i in range(1, 51):
            fn = FUNCS.get(i)
            if fn:
                fn(inp, s)
                time.sleep(0.3)
    else:
        fn = FUNCS.get(nomor)
        if fn:
            fn(inp, s)

    print()
    save_results(inp)
    print()
    print(Fore.CYAN + Style.BRIGHT + "  selesai. MR414N-ID" + Style.RESET_ALL)
    print()

if __name__ == "__main__":
    main()
