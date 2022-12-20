<template>
  <div class="space-y-20 py-16">
    <h1 class="text-8xl font-bold text-center text-white -space-x-5">
      <span>Z</span>
      <span>O</span>
      <span>R</span>
      <span>D</span>
      <span>L</span>
      <span>E</span>
    </h1>
    <word-component :wordToFind="displayTries" class="mx-auto"/>
  </div>
</template>

<script>
export default {
  name: 'Index',

  data() {
    return {
      defaultLetter: {
        letter: '',
        statut: 'NEUTRAL',
      },
      displayTries: [],
      letterFinded: [],
      currentLine: 0,
      currentLetter: 0,
      wordList: ['hello', 'candy', 'mouse', 'lack', 'photography', 'winter'],
    }
  },

  asyncData({ $axios }) {
    return $axios.$get('https://random-word-api.herokuapp.com/word')
      .then((response) => {
        return {
          word: response[0],
        }
      })
  },

  created() {
    this.createTries();
    window.addEventListener('keydown', this.doCommand);
    this.setFirstRow()
  },
  
  destroyed() {
    window.removeEventListener('keydown', this.doCommand);
  },

  methods: {
    async doCommand(e) {
      let cmd = String.fromCharCode(e.keyCode).toLowerCase();
      //si cmd est une lettre de l'alphabet
      if(cmd.match(/[a-z]/)) {
        //ajouter la lettre à la ligne courante
        if(this.currentLetter < this.word.length) {
          this.displayTries[this.currentLine][this.currentLetter].statut = 'ACTIVED';
          this.displayTries[this.currentLine][this.currentLetter].letter = cmd;
          this.currentLetter++;
        }
      }
      else if(e.keyCode == 13 && (this.currentLetter == this.displayTries[this.currentLine].length)) {
        //concaténer les lettres de la ligne courante
        let word = this.displayTries[this.currentLine].map(letter => letter.letter).join('');
        
        //call api https://api.dictionaryapi.dev/api/v2/entries/en/<word>
        const response = await fetch(`https://api.wordnik.com/v4/word.json/${word}/definitions?limit=200&includeRelated=false&useCanonical=false&includeTags=false&api_key=a2a73e7b926c924fad7001ca3111acd55af2ffabf50eb4ae5`);
        
        if(response.status == 200) {
          //verifier si le mot est trouvé
          if(this.isWordFound()) {
            alert('Congratulation!');
            window.location.reload(true)
          } else {
            //si la lettre est au bon endroit dans le mot son statut passe en ACCEPTED, si la lettre existe dans le mot il passe en FOUND sinon il passe en REJECTED
            for(let i = 0; i < this.displayTries[this.currentLine].length; i++) {
              let letter = this.displayTries[this.currentLine][i];
              if(letter.letter == this.word[i].toLocaleLowerCase()) {
                letter.statut = 'ACCEPTED';
                this.letterFinded[i].letter = letter.letter
              } else if(this.word.toLocaleLowerCase().indexOf(letter.letter) != -1) {
                letter.statut = 'FOUND';
              } else {
                letter.statut = 'REJECTED';
              }
            }
            
            //passer à la ligne suivante
            this.currentLine++;
            this.currentLetter = 0;
            if(this.currentLine >= 6) {
              alert('Failed sorry!');
              window.location.reload(true)
            }
            
            for(let i = 0; i < this.displayTries[this.currentLine].length; i++) {
              this.displayTries[this.currentLine][i].letter = structuredClone(this.letterFinded[i].letter)
            }
          }
        }
        else {
          //le mot n'existe pas
          this.$toast.error("This word is not accepted", { duration: 5000, position: "top-right" });
          for(let i = 0; i < this.displayTries[this.currentLine].length; i++) {
            if(this.currentLetter > 0) {
              this.currentLetter--;
              this.displayTries[this.currentLine][this.currentLetter].letter = '.';
              this.displayTries[this.currentLine][this.currentLetter].statut = 'NEUTRAL';
            }
          }
        }
      }
      else if (e.keyCode == 8) {
        //si la touche backspace est appuyée
        if(this.currentLetter > 0) {
          this.currentLetter--;
          this.displayTries[this.currentLine][this.currentLetter].letter = '.';
          this.displayTries[this.currentLine][this.currentLetter].statut = 'NEUTRAL';
        }
      }
    },

    isWordFound() {
      console.log("isWordFound")
      for(let i = 0; i < this.displayTries[this.currentLine].length; i++) {
        //vérifier que chaque lettre correspond à la lettre du mot à trouver
        if(this.displayTries[this.currentLine][i].letter != this.word[i].toLocaleLowerCase()) {
          return false;
        }
      }
      return true;
    },

    createTries(){
      for (let i = 0; i < 6; i++) {
        let row = []
        for (let j = 0; j < this.word.length; j++) {
          row.push({ ...this.defaultLetter })
        }
        this.letterFinded = structuredClone(row);
        this.displayTries.push(row)
      }
    },

    setFirstRow() {
      if(this.currentLine === 0) {
        for(let i = 0; i < this.displayTries[this.currentLine].length; i++) {
          if(this.displayTries[this.currentLine][i].letter == '') {
            this.displayTries[this.currentLine][i].letter = '.';
            this.letterFinded[i].letter = '.';
          }
        }
        this.displayTries[this.currentLine][0].letter = this.word[0].toLocaleLowerCase();
        this.letterFinded[0].letter = this.word[0].toLocaleLowerCase();
      }
    }
  }
}
</script>