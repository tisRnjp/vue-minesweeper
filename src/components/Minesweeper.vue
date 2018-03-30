
<template>
  <div class="main-board">
    <div class="game-title">
        <p>{{title}}</p>
    </div>
    <div class="game-board">
      <div class="board">
        <ul>
          <li v-for="tile in tiles" :key="tile.index">
            <div class="tile-size tile" v-on:contextmenu="plantFlag($event, tile)" v-on:click="openTile(tile)">
              <img class="tile-size tile-image" v-if="tile.mine && !tile.cover && !tile.flag" src="@/assets/mine.png" alt="mine" />
              <img class="tile-size tile-image" v-if="tile.flag" src="@/assets/flag.png" alt="flag" />
              <img class="tile-size" v-if="tile.cover" src="@/assets/tile.png" alt="cover" />
              <img class="tile-size tile-image" v-if="!tile.cover && tile.hint > 0" :src="'static/' + tile.hint + '.png'" alt="hint"/>
            </div>
          </li>
        </ul>
      </div>
    </div>
    <div class="game-title">
      <p v-if="wonGame">Congrats! You won.</p>
      <p v-if="gameOver">Sorry, you failed.</p>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
export default {
  name: "Minesweeper",
  data() {
    return {
      title: "Minesweeper",
      tiles: [],
      gameStatus: "Enjoy minesweeper",
      size: 10,
      flaggedTiles: [],
      wonGame: false,
      gameOver: false,

      // function to get random number
      getRandom: function() {
        return Math.floor(Math.random() * Math.floor(10));
      }
    };
  }, //  data ()

  // computed methods
  computed: {
    totalTile: function() {
      return this.size * this.size;
    }
  },

  methods: {
    // fill mines on board
    fillMine: function() {
      let mineCount = 0;
      let totalMine = 20;

      for (let i = 0; i < this.totalTile; i++) {
        if (this.getRandom() > 5 && mineCount <= totalMine) {
          this.tiles.push({
            index: i,
            mine: true,
            flag: false,
            cover: true,
            hint: 0
          });

          // count mine and break if count reached the total mine count
          mineCount += 1;

          continue;
        }

        this.tiles.push({
          index: i,
          mine: false,
          flag: false,
          cover: true,
          hint: 0
        });
      }
    },

    // fill hints on board
    fillHint: function() {
      let tilesChecked = "";
      for (let tile in this.tiles) {
        tile = this.tiles[tile];
        // if mine is found in the tile then fill the hints on surrounding tiles
        if (tile.mine) {
          // get the index of first surrounding tile of the mine tile (tiles checked from top right corner of mine tile)
          let checkIndex = tile.index - this.size - 1;
          // set left limit for each row when checking the tiles (Eg. for first row tile index should be >= 0)
          let leftLimit =
            parseInt(tile.index / this.size) * this.size - this.size;
          // set right limit for each row when checking the tiles (Eg. for first row tile index should be < 10)
          let rightLimit = leftLimit + this.size;

          // for loop to check surrounding tile of mine tile
          for (let i = 0; i < 3; i++) {
            for (let j = 0; j < 3; j++) {
              // tile index should be between 0 and total number of tiles
              if (checkIndex >= 0 && checkIndex < this.totalTile) {
                // tile index should be within limits for each row
                if (checkIndex >= leftLimit && checkIndex < rightLimit) {
                  // fill hints on tile that do not have mine
                  if (!this.tiles[checkIndex].mine) {
                    // add hints for each mine touched by the current tile
                    this.tiles[checkIndex].hint += 1;
                  }
                }
              }
              // game logic goes here
              checkIndex += 1;
            }
            // update left and right limit for next row
            leftLimit = rightLimit;
            rightLimit = leftLimit + this.size;

            checkIndex = checkIndex + this.size - 3;
          }
        }
      }
    },

    // flag tile when right mouse button is clicked
    plantFlag: function(event, selectedTile) {
      event.preventDefault();

      let tile = this.tiles[selectedTile.index];

      // right click only allowed when tile is flagged or still unturned
      if (tile.cover || tile.flag) {
        tile.cover = tile.flag;
        tile.flag = !tile.flag;
        // tile.mine = true;
      }

      // after flagging the tile check the tiles that were flagged
      this.flaggedTiles.push(tile.index);

      this.checkGame();

      return false;
    },

    // event listener for left mouse button
    openTile: function(selectedTile) {
      let tile = this.tiles[selectedTile.index];

      if (tile.cover) {
        tile.cover = false;
        if (tile.mine) {
          this.gameOver = true;
          this.gameStatus = "Game Over";
        }
      }

      this.checkGame();
    },

    // check if game is complete
    checkGame: function() {
      for (let i in this.tiles) {
        // if a covered tile or flagged tile does not have mine then the game is not complete
        if ((this.tiles[i].cover || this.tiles[i].flag) && !this.tiles[i].mine) {
          this.wonGame = false;
          console.log('mistaken mine: ' + i);
          break;
        } else {
          this.wonGame = true;
          this.gameStatus = "You Won";
          }
      }
    }
  },
  // instance hooks
  created: function() {
    this.fillMine();
    this.fillHint();
  } // created
};
// tile image credit: Icons made by <a href="http://www.freepik.com" title="Freepik">Freepik</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a> is licensed by <a href="http://creativecommons.org/licenses/by/3.0/" title="Creative Commons BY 3.0" target="_blank">CC 3.0 BY</a>
// Image Credit, Mine: <div>Icons made by <a href="https://www.flaticon.com/authors/creaticca-creative-agency" title="Creaticca Creative Agency">Creaticca Creative Agency</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a> is licensed by <a href="http://creativecommons.org/licenses/by/3.0/" title="Creative Commons BY 3.0" target="_blank">CC 3.0 BY</a></div>
// Image Credit, Flage: <div>Icons made by <a href="https://www.flaticon.com/authors/smashicons" title="Smashicons">Smashicons</a> from <a href="https://www.flaticon.com/" title="Flaticon">www.flaticon.com</a> is licensed by <a href="http://creativecommons.org/licenses/by/3.0/" title="Creative Commons BY 3.0" target="_blank">CC 3.0 BY</a></div>
</script>

<style scoped>
.board {
  width: 420px;
  height: 420px;
  background-color: blue;
  margin: 0 auto;
}

.board ul {
  margin: 0px;
  padding: 0px;
}

.board li {
  list-style-type: none;
  display: block;
  float: left;
  margin: 0px;
  padding: 0px;
}

.tile {
  background-color: #cccccc;
  border: 1px solid black;
}

.tile-size {
  width: 40px;
  height: 40px;
}

.tile-image {
  margin: 5px 5px;
  width: 30px;
  height: 30px;
}
</style>
