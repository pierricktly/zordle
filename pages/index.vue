<template>
  <div class="container mx-auto min-h-screen bg-green-500 flex flex-col justify-center items-center">
    <word-component :wordToFind="displayTries"/>
    {{ letterFinded }}
  </div>
</template>

<script>
export default {
  name: 'Index',

  data() {
    return {
      displayTries: [],
      letterFinded: [],
      currentLine: 0,
      currentLetter: 0,
      defaultLetter: {
        letter: '',
        isFound: false,
      }
    }
  },

  computed: {
    wordToFind() {
      return 'BONJOUR';
    }
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
    doCommand(e) {
      let cmd = String.fromCharCode(e.keyCode).toLowerCase();

      //si cmd est une lettre de l'alphabet
      if(cmd.match(/[a-z]/)) {
        console.log(cmd, "It's a letter", " | ", "currentLine", this.currentLine, " | ", "currentLetter", this.currentLetter);
        //ajouter la lettre à la ligne courante
        this.displayTries[this.currentLine][this.currentLetter].letter = cmd;
        this.displayTries[this.currentLine][this.currentLetter].statut = 'ACTIVED';
        this.currentLetter++;
        
        //si la lettre est la dernière de la ligne
        if(this.currentLetter == this.displayTries[this.currentLine].length) {
          //verifier si le mot est trouvé
          if(this.isWordFound()) {
            alert('Bravo !');
          } else {
            console.log("It's a letter");
            //si la lettre est au bon endroit dans le mot son statut passe en ACCEPTED, si la lettre existe dans le mot il passe en FOUND sinon il passe en REJECTED
            for(let i = 0; i < this.displayTries[this.currentLine].length; i++) {
              let letter = this.displayTries[this.currentLine][i];
              if(letter.letter == this.wordToFind[i].toLocaleLowerCase()) {
                letter.statut = 'ACCEPTED';
                this.letterFinded[i].letter = letter.letter
              } else if(this.wordToFind.toLocaleLowerCase().indexOf(letter.letter) != -1) {
                letter.statut = 'FOUND';
              } else {
                letter.statut = 'REJECTED';
              }
            }
            
            //passer à la ligne suivante
            this.currentLine++;
            this.currentLetter = 0;
            
            for(let i = 0; i < this.displayTries[this.currentLine].length; i++) {
              this.displayTries[this.currentLine][i].letter = structuredClone(this.letterFinded[i].letter)
            }
          }
        }
      } else if (e.keyCode == 8) {
        //si la touche backspace est appuyée
        if(this.currentLetter > 0) {
          this.currentLetter--;
          this.displayTries[this.currentLine][this.currentLetter].letter = '.';
          this.displayTries[this.currentLine][this.currentLetter].statut = 'NEUTRAL';
        }
      }
    },

    isWordFound() {
      for(let i = 0; i < this.displayTries[this.currentLine].length; i++) {
        //vérifier que chaque lettre correspond à la lettre du mot à trouver
        if(this.displayTries[this.currentLine][i].letter !== this.wordToFind[i]) {
          return false;
        }
      }
      return true;
    },

    createTries(){
      for (let i = 0; i < 6; i++) {
        let row = []
        for (let j = 0; j < this.wordToFind.length; j++) {
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
            this.displayTries[this.currentLine][i].letter = '. ';
            this.letterFinded[i].letter = '. ';
          }
        }
        this.displayTries[this.currentLine][0].letter = this.wordToFind[0];
        this.letterFinded[0].letter = this.wordToFind[0];
      }
    }
  }
}
</script>