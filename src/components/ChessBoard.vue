<template lang='pug'>
  .grid-wrap
    .grid-row(v-for="(row, r) in board") 
      .grid-item(v-for="(space, c) in row" :class="tileColor(r, c)" @click="piecePlacement(r, c)")
        div(:class="[space.color, activeTile(r, c)]" class="tile-overlay") {{space.icon}}
          span(v-if="r === 7" class="position-label-column") {{c}}
          span(v-if="c === 'A'" class="position-label-row") {{board.length - r}}
        div(class="tile-overlay" :class="{'available-tile' : space.available}")
</template>
<script>
export default {
    data() {
      return {
        selectedPiece: null,
        selectedSpace: { row: null, column: null },
        board: Array.from(new Array(8)).map((tile, index) => {
          return { 
            A: { color: null, icon: null, position: { row: index, column: 'A' }, id: null, available: false, hasMoved: false },
            B: { color: null, icon: null, position: { row: index, column: 'B' }, id: null, available: false, hasMoved: false },
            C: { color: null, icon: null, position: { row: index, column: 'C' }, id: null, available: false, hasMoved: false },
            D: { color: null, icon: null, position: { row: index, column: 'D' }, id: null, available: false, hasMoved: false },
            E: { color: null, icon: null, position: { row: index, column: 'E' }, id: null, available: false, hasMoved: false },
            F: { color: null, icon: null, position: { row: index, column: 'F' }, id: null, available: false, hasMoved: false },
            G: { color: null, icon: null, position: { row: index, column: 'G' }, id: null, available: false, hasMoved: false },
            H: { color: null, icon: null, position: { row: index, column: 'H' }, id: null, available: false, hasMoved: false },
          }
        }),
        whitePieces: [
          { color: 'white', icon: '♟', position: { row: 6, column: 'A' }, id: 'p1', available: false, hasMoved: false },
          { color: 'white', icon: '♟', position: { row: 6, column: 'B' }, id: 'p2', available: false, hasMoved: false },
          { color: 'white', icon: '♟', position: { row: 6, column: 'C' }, id: 'p2', available: false, hasMoved: false },
          { color: 'white', icon: '♟', position: { row: 6, column: 'D' }, id: 'p3', available: false, hasMoved: false },
          { color: 'white', icon: '♟', position: { row: 6, column: 'E' }, id: 'p4', available: false, hasMoved: false },
          { color: 'white', icon: '♟', position: { row: 6, column: 'F' }, id: 'p5', available: false, hasMoved: false },
          { color: 'white', icon: '♟', position: { row: 6, column: 'G' }, id: 'p6', available: false, hasMoved: false },
          { color: 'white', icon: '♟', position: { row: 6, column: 'H' }, id: 'p7', available: false, hasMoved: false },
          { color: 'white', icon: '♜', position: { row: 7, column: 'A' }, id: 'r1', available: false, hasMoved: false },
          { color: 'white', icon: '♞', position: { row: 7, column: 'B' }, id: 'n1', available: false, hasMoved: false },
          { color: 'white', icon: '♝', position: { row: 7, column: 'C' }, id: 'b1', available: false, hasMoved: false },
          { color: 'white', icon: '♛', position: { row: 7, column: 'D' }, id: 'q' , available: false, hasMoved: false },
          { color: 'white', icon: '♚', position: { row: 7, column: 'E' }, id: 'k' , available: false, hasMoved: false },
          { color: 'white', icon: '♝', position: { row: 7, column: 'F' }, id: 'b2', available: false, hasMoved: false },
          { color: 'white', icon: '♞', position: { row: 7, column: 'G' }, id: 'n2', available: false, hasMoved: false },
          { color: 'white', icon: '♜', position: { row: 7, column: 'H' }, id: 'r2', available: false, hasMoved: false },
        ],
        blackPieces: [
          { color: 'black', icon: '♟', position: { row: 1, column: 'A' }, id: 'p1', available: false, hasMoved: false },
          { color: 'black', icon: '♟', position: { row: 1, column: 'B' }, id: 'p2', available: false, hasMoved: false },
          { color: 'black', icon: '♟', position: { row: 1, column: 'C' }, id: 'p3', available: false, hasMoved: false },
          { color: 'black', icon: '♟', position: { row: 1, column: 'D' }, id: 'p4', available: false, hasMoved: false },
          { color: 'black', icon: '♟', position: { row: 1, column: 'E' }, id: 'p5', available: false, hasMoved: false },
          { color: 'black', icon: '♟', position: { row: 1, column: 'F' }, id: 'p6', available: false, hasMoved: false },
          { color: 'black', icon: '♟', position: { row: 1, column: 'G' }, id: 'p7', available: false, hasMoved: false },
          { color: 'black', icon: '♟', position: { row: 1, column: 'H' }, id: 'p8', available: false, hasMoved: false },
          { color: 'black', icon: '♜', position: { row: 0, column: 'A' }, id: 'r1', available: false, hasMoved: false },
          { color: 'black', icon: '♞', position: { row: 0, column: 'B' }, id: 'n1', available: false, hasMoved: false },
          { color: 'black', icon: '♝', position: { row: 0, column: 'C' }, id: 'b1', available: false, hasMoved: false },
          { color: 'black', icon: '♛', position: { row: 0, column: 'D' }, id: 'q' , available: false, hasMoved: false },
          { color: 'black', icon: '♚', position: { row: 0, column: 'E' }, id: 'k' , available: false, hasMoved: false },
          { color: 'black', icon: '♝', position: { row: 0, column: 'F' }, id: 'b2', available: false, hasMoved: false },
          { color: 'black', icon: '♞', position: { row: 0, column: 'G' }, id: 'n2', available: false, hasMoved: false },
          { color: 'black', icon: '♜', position: { row: 0, column: 'H' }, id: 'r2', available: false, hasMoved: false },
        ],
      }
    },
    created() {
      this.setupBoard();
    },
    methods: {
      activeTile(row, column) {
        if(this.selectedSpace){
          return row === this.selectedSpace.row && column === this.selectedSpace.column ? 'active-tile' : null;
        }
      },
      tileColor(row, column){
        const c = this.getColumnIndex(row, column);
        if(c % 2){
          return row % 2 ? 'white-tile' : 'black-tile';
        } else {
          return row % 2 ? 'black-tile' : 'white-tile';
        }
      },
      piecePlacement(row, column){
        
        this.selectedSpace = { row: row, column: column};
        switch(this.board[row][column].icon){
          case '♟':
            switch(this.board[row][column].color){
              case 'white':
                this.pawnMovement(row, column, -1);
                break;
              case 'black':
                this.pawnMovement(row, column, 1);
                break;
            }
            break;
          // case '♛':
          //   this.queenMovement(row, column);
          //   break;
          // case '♝':
          //   this.bishopMovement(row, column);
          //   break;
          // case '♞':
          //   this.knightMovement(row, column);
          //   break;
          // case '♜':
          //   this.rookMovement(row, column);
          //   break;
          case null:
            this.emptySpace(row, column);
            break;
        }
      },
      setupBoard(){
        this.whitePieces.forEach((piece, index) => {
          this.board[piece.position.row][piece.position.column] = piece;
        })
        this.blackPieces.forEach((piece, index) => {
          this.board[piece.position.row][piece.position.column] = piece;
        })
      },
      emptySpace(row, column){
        if(this.selectedPiece && this.board[row][column].available === true) {
          this.board[this.selectedPiece.position.row][this.selectedPiece.position.column] = { color: null, icon: null, position: { row: this.selectedPiece.position.row, column: this.selectedPiece.position.column }, id: null, available: false, hasMoved: false };
          this.selectedPiece.hasMoved = true;
          this.selectedPiece.position.row = row;
          this.selectedPiece.position.column = column;
          this.board[row][column] = this.selectedPiece;
          this.selectedPiece = null;
          this.selectedSpace = null;
          this.clearAvailableSpaces();
        } else {
          this.selectedPiece = null;
          this.selectedSpace = null;
          this.clearAvailableSpaces();
        }
      },
      clearAvailableSpaces() {
        this.board.forEach(row => { Reflect.ownKeys(row).forEach(space => { row[space].available = false }) });
      },
      getColumnIndex(row, column) {
        return Object.keys(this.board[row]).indexOf(column);
      },
      getColumnByIndex(column, val) {
        return Object.keys(this.board[0])[column + val];
      },
      pawnMovement(row, column, val) {
        this.clearAvailableSpaces();
        const c = this.getColumnIndex(row, column);
        if(!this.board[row][column].available) {
            if(this.board[row][column].hasMoved) {
              if(!this.board[row + val][column].color) {
                this.board[row + val][column].available = true;
                this.selectedPiece = this.board[row][column];
              } else {
                try{
                  this.board[row + val][this.getColumnByIndex(c, 1)].available = true;
                  this.board[row + val][this.getColumnByIndex(c, -1)].available = true;
                  this.selectedPiece = this.board[row][column];
                } catch(err) { }
              }
            } else {
              if(!this.board[row + val][column].color)
                this.board[row + val][column].available = true;
                if(!this.board[row + val][column].color){
                  this.board[row + (val + val)][column].available = true;
                }
                this.selectedPiece = this.board[row][column];
            }
        } else {
          this.emptySpace(row, column);
        }
      },
      knightMovement(index){
        if(!this.board[index].available) {
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
  top: 35px !important
  bottom: 35px !important
  left: 35px !important
  right: 35px !important
  animation: 1s pulse infinite
.position-label-column
  position: absolute
  font-size: 12px
  bottom: 10px
  right: 10px
  color: #123456
  -webkit-text-stroke: 0px #123456
.position-label-row
  -webkit-text-stroke: 0px #123456
  position: absolute
  font-size: 12px
  top: 10px
  left: 10px
  color: #123456
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
.white
  color: #ffff
  -webkit-text-stroke: .5px #123456
.black-tile
  transition: .3s ease all
  background: transparentize(#123456, .5)
.black
  color: #123456
  -webkit-text-stroke: .5px #123456
.white-tile
  background: white
@keyframes pulse
  0%
    transform: scale(1)
  50%
    transform: scale(.9)
  100%
    transform: scale(1)
</style>


