<template>
    <chess-board></chess-board>
</template>

<script>
// @ is an alias to /src
import ChessBoard from '@/components/ChessBoard.vue'

export default {
  name: 'home',
  components: {
    ChessBoard
  }
}
</script>
