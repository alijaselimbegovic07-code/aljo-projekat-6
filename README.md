#include <iostream>
#include <vector>
#include <fstream>
using namespace std;

class Narudzba {
private:
    int kolicina;
    bool potvrdjena;
    static int brojNarudzbi;

public:
    Narudzba() {
        kolicina = 1;
        potvrdjena = false;
        brojNarudzbi++;
    }

    Narudzba(int k, bool p) {
        if (k > 0)
            kolicina = k;
        else
            kolicina = 1;

        potvrdjena = p;
        brojNarudzbi++;
    }

    ~Narudzba() {
        brojNarudzbi--;
    }

    void setKolicina(int k) {
        if (k > 0)
            kolicina = k;
    }

    int getKolicina() {
        return kolicina;
    }

    void setPotvrdjena(bool p) {
        potvrdjena = p;
    }

    bool getPotvrdjena() {
        return potvrdjena;
    }

    static int getBrojNarudzbi() {
        return brojNarudzbi;
    }
};

int Narudzba::brojNarudzbi = 0;

class SistemNarudzbi {
private:
    vector<Narudzba*> narudzbe;

public:
    ~SistemNarudzbi() {
        for (int i = 0; i < narudzbe.size(); i++) {
            delete narudzbe[i];
        }
    }

    void dodaj(Narudzba* n) {
        narudzbe.push_back(n);
    }

    void prikazi() {
        if (narudzbe.size() == 0) {
            cout << "Nema unesenih narudzbi." << endl;
            return;
        }

        for (int i = 0; i < narudzbe.size(); i++) {
            cout << "Narudzba " << i + 1 << endl;
            cout << "Kolicina: " << narudzbe[i]->getKolicina() << endl;

            if (narudzbe[i]->getPotvrdjena())
                cout << "Potvrdjena" << endl;
            else
                cout << "Nije potvrdjena" << endl;

            cout << endl;
        }
    }

    void pretrazi(int trazenaKolicina) {
        bool pronadjena = false;

        for (int i = 0; i < narudzbe.size(); i++) {
            if (narudzbe[i]->getKolicina() == trazenaKolicina) {
                pronadjena = true;
                cout << "Pronadjena narudzba:" << endl;
                cout << "Kolicina: " << narudzbe[i]->getKolicina() << endl;

                if (narudzbe[i]->getPotvrdjena())
                    cout << "Potvrdjena" << endl;
                else
                    cout << "Nije potvrdjena" << endl;
            }
        }

        if (!pronadjena) {
            cout << "Narudzba nije pronadjena." << endl;
        }
    }

    void sacuvajUFajl() {
        ofstream fajl("narudzbe.txt");
        for (int i = 0; i < narudzbe.size(); i++) {
            fajl << narudzbe[i]->getKolicina() << " " << narudzbe[i]->getPotvrdjena() << endl;
        }
        fajl.close();
    }

    void ucitajIzFajla() {
        ifstream fajl("narudzbe.txt");
        int k;
        bool p;

        while (fajl >> k >> p) {
            Narudzba* n = new Narudzba(k, p);
            narudzbe.push_back(n);
        }

        fajl.close();
    }
};

int main() {
    SistemNarudzbi sistem;
    sistem.ucitajIzFajla();

    int broj, izbor;
    char odgovor;

    do {
        cout << "Unesi kolicinu: ";
        cin >> broj;

        while (broj <= 0) {
            cout << "Pogresan unos. Kolicina mora biti veca od 0: ";
            cin >> broj;
        }

        cout << "Da li je narudzba potvrdjena (d/n): ";
        cin >> odgovor;

        while (odgovor != 'd' && odgovor != 'n') {
            cout << "Pogresan unos. Unesi d ili n: ";
            cin >> odgovor;
        }

        bool stanje = false;
        if (odgovor == 'd')
            stanje = true;

        Narudzba* n = new Narudzba(broj, stanje);
        sistem.dodaj(n);

        cout << "1 - Da\n2 - Ne\n";
        cin >> izbor;

    } while (izbor == 1);

    sistem.sacuvajUFajl();

    cout << endl;
    cout << "Sve narudzbe:" << endl;
    sistem.prikazi();

    cout << "Ukupan broj aktivnih narudzbi: " << Narudzba::getBrojNarudzbi() << endl;

    cout << "Unesi kolicinu za pretragu: ";
    cin >> broj;
    sistem.pretrazi(broj);

    return 0;
}
