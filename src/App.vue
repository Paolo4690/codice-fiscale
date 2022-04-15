<template>
  <div id="app">
    <label for="nome">Nome</label>
    <input v-model.trim="nome" type="text" name="nome" id="nome"><br>
    <label for="cognome">Cognome</label>
    <input v-model.trim="cognome" type="text" name="cognome" id="cognome"><br>
    <label for="sesso">Sesso</label>
    <select v-model="sesso">
      <option value="M">M</option>
      <option value="F">F</option>
    </select><br>
    <label for="comune">Comune di nascita</label>
    <input v-model.trim="comune" type="text" name="comune" id="comune"><br>
    <label for="provincia">Provincia (Sigla)</label>
    <input v-model.trim="provincia" type="text" maxlength="2" name="provincia" id="provincia"><br>
    <label for="data">Data di nascita</label>
    <select v-model="giorno">
      <option v-for="giorno in $store.state.GG" :key="giorno" :value="giorno">{{ giorno }}</option>
    </select>
    <select v-model="mese">
      <option v-for="mese in $store.state.MM" :key="mese" :value="mese">{{ mese }}</option>
    </select>
    <select v-model="anno">
      <option v-for="anno in $store.state.YYYY" :key="anno" :value="anno">{{ anno }}</option>
    </select><br>
    <button @click="res()">Calcola Codice Fiscale</button>
    <h1>{{ codiceFiscale }}</h1>
  </div>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      nome:'',
      cognome:'',
      sesso:'',
      comune:'',
      provincia:'',
      giorno:'',
      mese:'',
      anno:'',
      codiceFiscale: '',
      somma: 0
    }
  },
  methods: {
    res() {
      this.codiceFiscale = '',
      this.surname()
      this.name()
      this.years()
      this.month()
      this.day()
      this.common()
      this.lastLetter()
    },
    surname() {
      let appoggioCognome = this.cognome.split(" ").join("")
      for (let i = 0; i < appoggioCognome.length; i++) {
        if (!appoggioCognome[i].toLowerCase().includes('a') && !appoggioCognome[i].toLowerCase().includes('e') && !appoggioCognome[i].toLowerCase().includes('i') && !appoggioCognome[i].toLowerCase().includes('o') && !appoggioCognome[i].toLowerCase().includes('u')) {
          if (this.codiceFiscale.length < 3) {
            this.codiceFiscale += appoggioCognome[i].toUpperCase()
          }
        }
      }
    },
    name() {
      let appoggioNome = []
      this.nome = this.nome.split(" ").join("")
      for (let i = 0; i < this.nome.length; i++) {
        if (appoggioNome.length < 4 && !this.nome[i].toLowerCase().includes('a') && !this.nome[i].toLowerCase().includes('e') && !this.nome[i].toLowerCase().includes('i') && !this.nome[i].toLowerCase().includes('o') && !this.nome[i].toLowerCase().includes('u')) {
          appoggioNome.push(this.nome[i].toUpperCase())
        }
      }
      if (appoggioNome.length <= 3 && this.codiceFiscale.length < 6) {
        this.codiceFiscale += appoggioNome.join('')
      } else if (appoggioNome.length > 3 && this.codiceFiscale.length < 6) {
        this.codiceFiscale += appoggioNome[0] + appoggioNome[2] + appoggioNome[3]
      }

      if (this.codiceFiscale.length < 6) {
        for (let i = 0; i < this.nome.length; i++) {
          if (this.nome[i].toLowerCase().includes('a') || this.nome[i].toLowerCase().includes('e') || this.nome[i].toLowerCase().includes('i') || this.nome[i].toLowerCase().includes('o') || this.nome[i].toLowerCase().includes('u')) {
            if (this.codiceFiscale.length < 6) {
              this.codiceFiscale += this.nome[i].toUpperCase()
            }
          }
        }
      }
    },
    years() {
      if (this.codiceFiscale.length < 8) {
      this.codiceFiscale += this.anno.substring(2, 4)
      }
    }, 
    month() {
      if (this.codiceFiscale.length < 9) {
        switch (this.mese) {
          case '01':
            this.codiceFiscale += 'A'
            break
          case '02':
            this.codiceFiscale += 'B'
            break
          case '03':
            this.codiceFiscale += 'C'
            break
          case '04':
            this.codiceFiscale += 'D'
            break
          case '05':
            this.codiceFiscale += 'E'
            break
          case '06':
            this.codiceFiscale += 'H'
            break
          case '07':
            this.codiceFiscale += 'L'
            break
          case '08':
            this.codiceFiscale += 'M'
            break
          case '09':
            this.codiceFiscale += 'P'
            break
          case '10':
            this.codiceFiscale += 'R'
            break
          case '11':
            this.codiceFiscale += 'S'
            break
          case '12':
            this.codiceFiscale += 'T'
            break
        }
      }
    },
    day() {
      if(this.sesso == 'M') {
        if (this.codiceFiscale.length < 11) {
          this.codiceFiscale += this.giorno
        }
      } else {
        if (this.codiceFiscale.length < 11) {
          this.codiceFiscale += parseInt(this.giorno) + 40
        }
      }
    },
    common() {
      for (let i = 0; i < this.$store.state.codiceComune.length; i++) {
        if (this.$store.state.codiceComune[i].nome.toLowerCase() === this.comune.toLowerCase() && this.$store.state.codiceComune[i].sigla.toLowerCase() === this.provincia.toLowerCase()) {
          this.codiceFiscale += this.$store.state.codiceComune[i].codiceCatastale
        }
      }
    },
    lastLetter() {
      this.somma = 0
      for (let i = 0; i < this.codiceFiscale.length; i++) {
        if ( i % 2 == 0) {

          for (let y = 0; y < this.$store.state.logicForLastLetter.length; y++) {
            if (this.codiceFiscale[i].toString().toLowerCase() === this.$store.state.logicForLastLetter[y].value) {
              this.somma += this.$store.state.logicForLastLetter[y].dispari
            }
          }
        } else {
          for (let y = 0; y < this.$store.state.logicForLastLetter.length; y++) {
            if (this.codiceFiscale[i].toString().toLowerCase() === this.$store.state.logicForLastLetter[y].value) {
              this.somma += this.$store.state.logicForLastLetter[y].pari
            }
          }
        }
      }
      this.codiceFiscale += this.$store.state.lastLetter[this.somma % 26]
    }
  }
}
</script>

<style lang="scss">

</style>
