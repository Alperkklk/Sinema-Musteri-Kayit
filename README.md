import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;

import java.io.File;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

// Base Entity Class
abstract class BaseEntity {
    private String id;

    public BaseEntity(String id) {
        this.id = id;
    }

    public String getId() {
        return id;
    }

    public abstract String bilgiGoster();
}

// Interface
interface IKayit {
    void kaydet(String fileName) throws IOException;
}

// Customer Class
class Musteri extends BaseEntity implements IKayit {
    private String ad;
    private String soyad;

    public Musteri(String id, String ad, String soyad) {
        super(id);
        this.ad = ad;
        this.soyad = soyad;
    }

    public String getAd() {
        return ad;
    }

    public String getSoyad() {
        return soyad;
    }

    @Override
    public String bilgiGoster() {
        return "Müşteri ID: " + getId() + ", Ad: " + ad + ", Soyad: " + soyad;
    }

    @Override
    public void kaydet(String fileName) throws IOException {
        ObjectMapper mapper = new ObjectMapper();
        mapper.enable(SerializationFeature.INDENT_OUTPUT);
        mapper.writeValue(new File(fileName), this);
    }
}

// Movie Class
class Film extends BaseEntity implements IKayit {
    private String ad;
    private int sure;
    private String tur;

    public Film(String id, String ad, int sure, String tur) {
        super(id);
        this.ad = ad;
        this.sure = sure;
        this.tur = tur;
    }

    public String getAd() {
        return ad;
    }

    public int getSure() {
        return sure;
    }

    public String getTur() {
        return tur;
    }

    @Override
    public String bilgiGoster() {
        return "Film ID: " + getId() + ", Ad: " + ad + ", Süre: " + sure + " dk, Tür: " + tur;
    }

    @Override
    public void kaydet(String fileName) throws IOException {
        ObjectMapper mapper = new ObjectMapper();
        mapper.enable(SerializationFeature.INDENT_OUTPUT);
        mapper.writeValue(new File(fileName), this);
    }
}

// Salon Class
class Salon extends BaseEntity implements IKayit {
    private String isim;
    private Film film;
    private List<Musteri> musteriler;

    public Salon(String id, String isim, Film film) {
        super(id);
        this.isim = isim;
        this.film = film;
        this.musteriler = new ArrayList<>();
    }

    public String getIsim() {
        return isim;
    }

    public Film getFilm() {
        return film;
    }

    public List<Musteri> getMusteriler() {
        return musteriler;
    }

    public void musteriEkle(Musteri musteri) {
        musteriler.add(musteri);
    }

    @Override
    public String bilgiGoster() {
        StringBuilder sb = new StringBuilder();
        sb.append("Salon ID: ").append(getId()).append(", İsim: ").append(isim).append(", Film: ").append(film.getAd()).append("\nMüşteriler:\n");
        for (Musteri m : musteriler) {
            sb.append(m.bilgiGoster()).append("\n");
        }
        return sb.toString();
    }

    @Override
    public void kaydet(String fileName) throws IOException {
        ObjectMapper mapper = new ObjectMapper();
        mapper.enable(SerializationFeature.INDENT_OUTPUT);
        mapper.writeValue(new File(fileName), this);
    }
}

// Main Class
public class SinemaKayitSistemi {
    public static void main(String[] args) throws IOException {
        // Create instances
        Musteri musteri1 = new Musteri("1", "Ali", "Kaya");
        Musteri musteri2 = new Musteri("2", "Ayşe", "Demir");

        Film film1 = new Film("1", "Inception", 148, "Bilim Kurgu");
        Film film2 = new Film("2", "Titanic", 195, "Romantik");

        Salon salon1 = new Salon("1", "Salon A", film1);
        salon1.musteriEkle(musteri1);
        salon1.musteriEkle(musteri2);

        // Save data to JSON files
        musteri1.kaydet("Musteri1.json");
        musteri2.kaydet("Musteri2.json");

        film1.kaydet("Film1.json");
        film2.kaydet("Film2.json");

        salon1.kaydet("Salon1.json");

        // Display information
        System.out.println(film1.bilgiGoster());
        System.out.println(salon1.bilgiGoster());
    }
}
