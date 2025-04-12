<template>
    <div class="container">
      <div id="korisnik-podaci"></div>
      <div id="kosarica"></div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        podaci: {
          slike: {
            "Jabuka": "https://www.svgrepo.com/show/530203/apple.svg",
            "Mrkva": "https://www.svgrepo.com/show/530216/carrot.svg",
            "Sir": "https://www.svgrepo.com/show/530219/cake.svg",
            "Kruh": "https://www.svgrepo.com/show/530223/bread.svg",
          },
          proizvodi: [
            { naziv: "Jabuka", cijena: 0.25 },
            { naziv: "Mrkva", cijena: 0.12 },
            { naziv: "Kruh", cijena: 2.00 },
            { naziv: "Sir", cijena: 4.48 }
          ],
          korisnik: {
            jeAdmin: true,
            osobni_podaci: {
              ime: "Marko",
              prezime: "Krivić",
              adresa: {
                grad: "Pula",
                ulica: "Veruda",
                broj: 32
              },
              broj_telefona: "+091-123-456"
            },
            kosarica: [
              { naziv: "Jabuka", količina: 4 },
              { naziv: "Mrkva", količina: 12 },
              { naziv: "Sir", količina: 1 },
              { naziv: "Kruh", količina: 3 },
            ]
          }
        }
      }
    },
    methods: {
      dohvatiCijenu(naziv) {
        return this.podaci.proizvodi.find(p => p.naziv === naziv)?.cijena || 0;
      },
      izracunajUkupno() {
        return this.podaci.korisnik.kosarica.reduce((suma, stavka) => 
          suma + (this.dohvatiCijenu(stavka.naziv) * stavka.količina), 0);
      },
      najskupljaStavka() {
        return this.podaci.korisnik.kosarica.reduce((najskuplja, stavka) => {
          const ukupno = this.dohvatiCijenu(stavka.naziv) * stavka.količina;
          return ukupno > najskuplja.ukupno ? { naziv: stavka.naziv, ukupno } : najskuplja;
        }, { naziv: "", ukupno: 0 }).naziv;
      },
      prikaziPodatke() {      
        const korisnikDiv = document.getElementById('korisnik-podaci');
        korisnikDiv.innerHTML = `
          <h2>Korisnički podaci</h2>
          <p><strong>Ime:</strong> ${this.podaci.korisnik.osobni_podaci.ime} ${this.podaci.korisnik.osobni_podaci.prezime}</p>
          <p><strong>Adresa:</strong> ${this.podaci.korisnik.osobni_podaci.adresa.ulica} ${this.podaci.korisnik.osobni_podaci.adresa.broj}, ${this.podaci.korisnik.osobni_podaci.adresa.grad}</p>
          <p><strong>Telefon:</strong> ${this.podaci.korisnik.osobni_podaci.broj_telefona}</p>
        `;
        
        if (this.podaci.korisnik.jeAdmin) {
          korisnikDiv.classList.add('admin');
        }
  
        
        const kosaricaDiv = document.getElementById('kosarica');
        kosaricaDiv.innerHTML = '<h2>Košarica</h2>';
        
        const najskuplja = this.najskupljaStavka();
        const stavkeDiv = document.createElement('div');
        
        this.podaci.korisnik.kosarica.forEach(stavka => {
          const cijena = this.dohvatiCijenu(stavka.naziv);
          const ukupno = cijena * stavka.količina;
          const jeNajskuplja = stavka.naziv === najskuplja;
          
          const stavkaDiv = document.createElement('div');
          stavkaDiv.className = `stavka ${jeNajskuplja ? 'najskuplja' : ''}`;
          stavkaDiv.innerHTML = `
            <img src="${this.podaci.slike[stavka.naziv]}" alt="${stavka.naziv}">
            <div>
              <div class="naziv">${stavka.naziv}</div>
              <div>Cijena: ${cijena.toFixed(2)}€ | Količina: ${stavka.količina}</div>
              <div>Ukupno: ${ukupno.toFixed(2)}€</div>
            </div>
          `;
          stavkeDiv.appendChild(stavkaDiv);
        });
        
        kosaricaDiv.appendChild(stavkeDiv);
        
        const ukupnoDiv = document.createElement('div');
        ukupnoDiv.className = 'ukupno';
        ukupnoDiv.innerHTML = `Ukupna cijena: ${this.izracunajUkupno().toFixed(2)}€`;
        kosaricaDiv.appendChild(ukupnoDiv);
      }
    },
    mounted() {
      this.prikaziPodatke();
    }
  }
  </script>
  
  <style>
  .container {
    max-width: 600px;
    margin: 0 auto;
    padding: 20px;
    font-family: Arial, sans-serif;
  }
  
  #korisnik-podaci {
    margin-bottom: 20px;
    padding: 15px;
    border: 1px solid #ccc;
    border-radius: 5px;
  }
  
  .admin {
    color: blue;
  }
  
  #kosarica {
    padding: 15px;
    border: 1px solid #eee;
    border-radius: 5px;
  }
  
  .stavka {
    display: flex;
    align-items: center;
    margin-bottom: 10px;
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
  }
  
  .stavka img {
    width: 50px;
    height: 50px;
    margin-right: 15px;
  }
  
  .najskuplja {
    background-color: #ffebee;
    border-color: #ffcdd2;
  }
  
  .najskuplja .naziv {
    color: red;
    font-weight: bold;
  }
  
  .ukupno {
    font-weight: bold;
    margin-top: 20px;
    text-align: right;
    font-size: 1.2em;
  }
  </style>