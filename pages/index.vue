<template>
  <div class="container mx-auto min-h-screen bg-green-500 flex justify-center items-center">
    <word-component :wordToFind="displayTries"/>
  </div>
</template>

<script>
export default {
  name: 'Index',

  data() {
    return {
      displayTries: [],
      currentLine: 0,
      currentLetter: 0,
      defaultLetter: {
        letter: '.',
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
  },
  
  destroyed() {
    window.removeEventListener('keydown', this.doCommand);
  },

  methods: {
    doCommand(e) {
      console.log('keyCode', e.keyCode);
      console.log('which', e.which);
      console.log('e', e);
      let cmd = String.fromCharCode(e.keyCode).toLowerCase();
      console.log(cmd);

      //si cmd est une lettre de l'alphabet
      if(cmd.match(/[a-z]/)) {
        //ajouter la lettre à la ligne courante
        this.displayTries[this.currentLine][this.currentLetter].letter = cmd;
        this.currentLetter++;
        
        //si la lettre est la dernière de la ligne
        if(this.currentLetter == this.displayTries[this.currentLine].length) {
          //verifier si le mot est trouvé
          if(this.isWordFound()) {
            alert('Bravo !');
          } else {
            //si la lettre est au bon endroit dans le mot son statut passe en ACCEPTED, si la lettre existe dans le mot il passe en FOUND sinon il passe en REJECTED
            for(let i = 0; i < this.displayTries[this.currentLine].length; i++) {
              let letter = this.displayTries[this.currentLine][i];
              if(letter.letter == this.wordToFind[i].toLocaleLowerCase()) {
                letter.statut = 'ACCEPTED';
              } else if(this.wordToFind.toLocaleLowerCase().indexOf(letter.letter) != -1) {
                letter.statut = 'FOUND';
              } else {
                letter.statut = 'REJECTED';
              }
            }
            
            //passer à la ligne suivante
            this.currentLine++;
            this.currentLetter = 0;
            //copier les lettres correct à la ligne d'en dessous
            for(let i = 0; i < this.displayTries[this.currentLine].length; i++) {
              console.log(this.displayTries[this.currentLine-1])
              if(this.displayTries[this.currentLine-1][i].statut == 'ACCEPTED') {
                this.displayTries[this.currentLine][i].letter = this.displayTries[this.currentLine-1][i].letter;
              }
            }
          }
        }
      } else if (e.keyCode == 8) {
        //si la touche backspace est appuyée
        if(this.currentLetter > 0) {
          this.currentLetter--;
          this.displayTries[this.currentLine][this.currentLetter].letter = this.defaultLetter.letter;
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
        this.displayTries.push(row)
      }
    }
  }
}
</script>