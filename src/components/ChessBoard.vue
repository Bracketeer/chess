<template lang='pug'>
  .grid-wrap
    .grid-item(v-for="(space, s) in board" :class="tileColor(s)" @click="piecePlacement(s)")
      div(:class="[space.color, activeTile(s)]" class="tile-overlay") {{space.icon}}
      div(class="tile-overlay" :class="{'available-tile' : space.available}")
</template>
<script>
export default {
    data() {
      return {
        selectedPiece: null,
        selectedSpace: null,
        board: null,
        whitePieces: [
          { color:'white', icon: '♟', position: 8,  id: 'p1', available: false, hasMoved: false },
          { color:'white', icon: '♟', position: 9,  id: 'p2', available: false, hasMoved: false },
          { color:'white', icon: '♟', position: 10, id: 'p3', available: false, hasMoved: false },
          { color:'white', icon: '♟', position: 11, id: 'p4', available: false, hasMoved: false },
          { color:'white', icon: '♟', position: 12, id: 'p5', available: false, hasMoved: false },
          { color:'white', icon: '♟', position: 13, id: 'p6', available: false, hasMoved: false },
          { color:'white', icon: '♟', position: 14, id: 'p7', available: false, hasMoved: false },
          { color:'white', icon: '♟', position: 15, id: 'p8', available: false, hasMoved: false },
          { color:'white', icon: '♜', position: 0,  id: 'r1', available: false, hasMoved: false },
          { color:'white', icon: '♞', position: 1,  id: 'n1', available: false, hasMoved: false },
          { color:'white', icon: '♝', position: 2,  id: 'b1', available: false, hasMoved: false },
          { color:'white', icon: '♛', position: 3,  id: 'q' , available: false, hasMoved: false },
          { color:'white', icon: '♚', position: 4,  id: 'k' , available: false, hasMoved: false },
          { color:'white', icon: '♝', position: 5,  id: 'b2', available: false, hasMoved: false },
          { color:'white', icon: '♞', position: 6,  id: 'n2', available: false, hasMoved: false },
          { color:'white', icon: '♜', position: 7,  id: 'r2', available: false, hasMoved: false },
        ],
        blackPieces: [
          { color:'black', icon: '♟', position: 48, id: 'p1', available: false, hasMoved: false },
          { color:'black', icon: '♟', position: 49, id: 'p2', available: false, hasMoved: false },
          { color:'black', icon: '♟', position: 50, id: 'p3', available: false, hasMoved: false },
          { color:'black', icon: '♟', position: 51, id: 'p4', available: false, hasMoved: false },
          { color:'black', icon: '♟', position: 52, id: 'p5', available: false, hasMoved: false },
          { color:'black', icon: '♟', position: 53, id: 'p6', available: false, hasMoved: false },
          { color:'black', icon: '♟', position: 54, id: 'p7', available: false, hasMoved: false },
          { color:'black', icon: '♟', position: 55, id: 'p8', available: false, hasMoved: false },
          { color:'black', icon: '♜', position: 56, id: 'r1', available: false, hasMoved: false },
          { color:'black', icon: '♞', position: 57, id: 'n1', available: false, hasMoved: false },
          { color:'black', icon: '♝', position: 58, id: 'b1', available: false, hasMoved: false },
          { color:'black', icon: '♛', position: 59, id: 'q' , available: false, hasMoved: false },
          { color:'black', icon: '♚', position: 60, id: 'k' , available: false, hasMoved: false },
          { color:'black', icon: '♝', position: 61, id: 'b2', available: false, hasMoved: false },
          { color:'black', icon: '♞', position: 62, id: 'n2', available: false, hasMoved: false },
          { color:'black', icon: '♜', position: 63, id: 'r2', available: false, hasMoved: false },
        ],
      }
    },
    created() {
      this.createBoard();
    },
    methods: {
      activeTile(i){
        return i === this.selectedSpace ? 'active-tile' : null;
      },
      tileColor(i){
        if(
          (i >= 0 && i <= 7 && i % 2) || 
          (i >= 16 && i <= 23 && i % 2) ||
          (i >= 32 && i <= 39 && i % 2) ||
          (i >= 48 && i <= 55 && i % 2) 
          ) {
            return 'black-tile'
        } else if(
          (i >= 8 && i <= 15 && (i + 1) % 2) || 
          (i >= 24 && i <= 31 && (i + 1) % 2) ||
          (i >= 40 && i <= 47 && (i + 1) % 2) ||
          (i >= 56 && i <= 63 && (i + 1) % 2)
        ){
          return 'black-tile'
        } else {
          return 'white-tile'
        }
      },
      piecePlacement(index){
        this.selectedSpace = index;
        switch(this.board[index].icon){
          case '♟':
            this.pawnMovement(index);
            break;
          case '♛':
            this.queenMovement(index);
            break;
          case '♝':
            this.bishopMovement(index);
            break;
          case '♞':
            this.knightMovement(index);
            break;
          case '♜':
            this.rookMovement(index);
            break;
          case null:
            this.emptySpace(index);
            break;
        }
      },
      emptySpace(index){
        if(this.selectedPiece && this.board[index].available === true) {
          let pieceIndex = this.board.indexOf(this.selectedPiece);
          this.board[pieceIndex] = { color: null, icon: null, position: index,  id: null, available: false };
          this.selectedPiece.hasMoved = true;
          this.board[index] = this.selectedPiece;
          this.selectedPiece = null;
          this.selectedSpace = null;
          this.board.map(space => space.available = false)
        } else {
          this.selectedPiece = null;
          this.selectedSpace = null;
          this.board.map(space => space.available = false)
        }
      },
      pawnMovement(index){
        if(!this.board[index].available) {
        switch(this.board[index].color) {
          case 'white':
            if(this.board[index].hasMoved) {
              this.board.map((space, s) => s === index + 8 ? space.available = true : space.available = false)
              this.selectedPiece = this.board[index];
              break;
            } else {
              this.board.map((space, s) => s === index + 8 || s === index + 16  ? space.available = true : space.available = false);
              this.selectedPiece = this.board[index];
              break;
            }
          case 'black':
            if(this.board[index].hasMoved) {
              this.board.map((space, s) => s === index - 8 ? space.available = true : space.available = false)
              this.selectedPiece = this.board[index];
              break;
            } else {
              this.board.map((space, s) => s === index - 8 || s === index - 16  ? space.available = true : space.available = false);
              this.selectedPiece = this.board[index];
              break;
            }
          }
        } else {
          this.emptySpace(index);
        }
      },
      knightMovement(index){
        if(!this.board[index].available) {
            this.board.map((space, s) => {
              if(
                this.moveableSpace(s, index + 15) ||
                this.moveableSpace(s, index - 15) ||
                this.moveableSpace(s, index + 17) ||
                this.moveableSpace(s, index - 17) ||
                this.moveableSpace(s, index + 10) ||
                this.moveableSpace(s, index - 10) ||
                this.moveableSpace(s, index + 6)  ||
                this.moveableSpace(s, index - 6)
                ) { 
                  space.available = true
                } else { 
                  space.available = false
                }
                this.selectedPiece = this.board[index];
          })
        } else {
          this.emptySpace(index);
        }
      },
      queenMovement(index) {
        if(!this.board[index].available) {

        } else {
          this.emptySpace(index);
        }
      },
      bishopMovement(index) {
        if(!this.board[index].available) {

        } else {
          this.emptySpace(index);
        }
      },
      rookMovement(index) {
        if(!this.board[index].available) {
          } else {
            this.emptySpace(index);
        }
      },
      moveableSpace(space, index){
        return space === index && this.board[index].color !== this.selectedPiece.color ? true : false;
      },
      createBoard(){
        this.board = Array.from(new Array(64)).map((space, index) => {
          let whitePosition = this.whitePieces.filter(piece => index === piece.position);
          let blackPosition = this.blackPieces.filter(piece => index === piece.position);
          if(whitePosition.length > 0) { 
            return whitePosition[0];
          } else if(blackPosition.length > 0){
            return blackPosition[0];
          } else { 
            return { color: null, icon: null, position: index,  id: null, available: false };
          }
        })
      },
    }
  };
</script>
<style lang='sass'>
*
  box-sizing: border-box
#app, html, body
  height: 100%
  width: 100%
  overflow: hidden
  display: flex
  justify-content: center
  align-items: center
.grid-wrap
  display: grid
  grid-template-columns: repeat(8, 100px)
  grid-template-rows: repeat(1, 100px)
  width: 802px
  height: 802px
  margin: auto
  justify-content: center
  border: 1px solid transparentize(#000, .5)
  grid-gap: 0 0
.grid-row
  display: grid
  grid-template-columns: repeat(8, 100px)
  
.grid-item
  cursor: pointer
  height: 100px
  width: 100px
  display: flex
  align-items: center
  justify-content: center
  font-size: 3rem
  position: relative
  &:hover
    border: 2px solid tomato
.active-tile
  background: transparentize(tomato, .5) !important
.available-tile
  background: transparentize(tomato, .8) !important
  border-radius: 50%
  top: 30px !important
  bottom: 30px !important
  left: 30px !important
  right: 30px !important
.tile-overlay
  position: absolute
  top: 0
  left: 0
  right: 0
  bottom: 0
  z-index: 5
  display: flex
  justify-content: center
  align-items: center
  transition: .3s ease all
.black
  color: #ffff
  -webkit-text-stroke: .5px #123456
.black-tile
  transition: .3s ease all
  background: transparentize(#123456, .5)
.white
  color: #123456
  -webkit-text-stroke: .5px #123456
.white-tile
  background: white
</style>


