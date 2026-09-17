# Pertemuan 3 - PBO

Ringkasan singkat: materi ini membahas constructor berdelegasi, anggota statis, dan konstanta dalam Java.

## Main.java
```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Jumlah rekening di awal: " + RekeningBank.getJumlahRekening());

        RekeningBank a = new RekeningBank("111", "Ani", 1_000_000);
        RekeningBank b = new RekeningBank("222", "Budi");
        RekeningBank c = new RekeningBank("333", "Citra", 250_000);

        System.out.println(a);
        System.out.println(b);
        System.out.println(c);

        System.out.println("Jumlah rekening sekarang: " + RekeningBank.getJumlahRekening()
                           + "   (seharusnya 3, bukan 4)");

        System.out.println();
        System.out.println("=== Operasi ===");
        a.setor(500_000);
        System.out.println("Setelah setor 500.000  -> " + a);

        try {
            a.tarik(9_999_999);
            System.out.println("  MASALAH: penarikan melebihi batas seharusnya ditolak!");
        } catch (RuntimeException e) {
            System.out.println("  Ditolak: " + e.getMessage());
        }

        b.potongBiayaAdmin();
        System.out.println("Budi setelah potong admin: " + b + "   (saldo tidak boleh negatif)");

        System.out.printf("Bunga setahun dari saldo Ani: Rp%,.2f%n",
                RekeningBank.bungaSetahun(a.getSaldo()));
    }
}
```

## RekeningBank.java
```java
/**
 * Sesi 3 — constructor berdelegasi, anggota statis, dan konstanta.
 */
public class RekeningBank {

    public static final double BUNGA_TAHUNAN = 0.025;
    public static final double BIAYA_ADMIN = 5000;
    public static final double BATAS_TARIK_SEKALI = 5000000;

    private static int jumlahRekening = 0;

    private final String nomor;
    private final String pemilik;
    private double saldo;

    public RekeningBank(String nomor, String pemilik) {
        this(nomor, pemilik, 0);
    }

    public RekeningBank(String nomor, String pemilik, double saldoAwal) {
        if (nomor == null || nomor.isBlank()) {
            throw new IllegalArgumentException("Nomor rekening tidak boleh kosong");
        }
        if (saldoAwal < 0) {
            throw new IllegalArgumentException("Saldo awal tidak boleh negatif");
        }

        this.nomor = nomor;
        this.pemilik = pemilik;
        this.saldo = saldoAwal;
        jumlahRekening++;
    }

    public void setor(double jumlah) {
        if (jumlah <= 0) {
            throw new IllegalArgumentException("Jumlah setoran harus positif");
        }
        saldo += jumlah;
    }

    public void tarik(double jumlah) {
        if (jumlah <= 0) {
            throw new IllegalArgumentException("Jumlah penarikan harus positif");
        }
        if (jumlah > saldo) {
            throw new IllegalArgumentException("Saldo tidak mencukupi");
        }
        if (jumlah > BATAS_TARIK_SEKALI) {
            throw new IllegalArgumentException("Melebihi batas penarikan sekali transaksi");
        }
        saldo -= jumlah;
    }

    public void potongBiayaAdmin() {
        saldo = Math.max(0, saldo - BIAYA_ADMIN);
    }

    public static int getJumlahRekening() {
        return jumlahRekening;
    }

    public static double bungaSetahun(double pokok) {
        return pokok * BUNGA_TAHUNAN;
    }

    public double getSaldo() { return saldo; }
    public String getNomor() { return nomor; }

    @Override
    public String toString() {
        return String.format("Rekening[%s] %-14s Rp%,.2f", nomor, pemilik, saldo);
    }
}
```

## Catatan
- saldo tidak boleh negatif
- nomor rekening tetap setelah dibuat
- setoran dan penarikan harus bernilai positif
