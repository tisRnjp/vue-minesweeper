
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
              <img class="tile-size tile-mine" v-if="tile.mine && !tile.cover" src="@/assets/mine.png" alt="mine" />
              <img class="tile-size tile-flag" v-if="tile.flag" src="@/assets/flag.png" alt="flag" />
              <img class="tile-size" v-if="tile.cover" src="@/assets/tile.png" alt="cover" />
              <!-- <img class="tile-size" v-if="land" src="" alt="land" /> -->
            </div>
          </li>
        </ul>
      </div>
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

    // function to get random number
    getRandom: function() {
      return Math.floor(Math.random() * Math.floor(10));
    }
  };
}, //  data ()

// computed methods
computed: {
  totalTile: function () {
    return this.size * this.size;
  }
},

methods: {
  // fill mines on board
  fillMine: function() {
    let mineCount = 0;
    let totalMine = 20;

    let test

    for (let i = 0; i < this.totalTile; i++) {
      if (this.getRandom() > 5 && mineCount <= totalMine) {
        this.tiles.push({
          index: i,
          mine: true,
          flag: false,
          cover: true,
          hint: 0
        });

        test = test + "," + i

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

    console.log('mines ' + test)
  },

  // fill hints on board
  fillHint: function () {
    let tilesChecked = '';
    for (let tile in this.tiles) {
      tile = this.tiles[tile]
      if (tile.mine) {
        let checkIndex = tile.index - this.size - 1;
        for (let i = 0; i < 3; i++) {
          let leftLimit = Math.abs(parseInt(checkIndex/10) * 10 )
          if (checkIndex >= leftLimit && checkIndex < (leftLimit + this.size)) {
            tilesChecked = tilesChecked + ' ' + tile.index;
          for (let j = 0; j < 3; j++) {
            if (checkIndex >= 0 && checkIndex < this.totalTile) {
                this.tiles[checkIndex].hint += 1
                console.log("checkIndex " + checkIndex)
              }
              // game logic goes here
              checkIndex += 1;
            }
            checkIndex = checkIndex + this.size - 3;
            }
        }
      }
    }
    // console.log(this.tiles);
    console.log(tilesChecked);
  },

  // flag tile when right mouse button is clicked
  plantFlag: function (event, selectedTile) {
    event.preventDefault();

    let tile = this.tiles[selectedTile.index];

    // right click only allowed when tile is flagged or still unturned
    if (tile.cover || tile.flag) {
      tile.cover = tile.flag;
      tile.flag = !tile.flag;
      tile.mine = false;
    }

    return false;
  },

  // event listener for left mouse button
  openTile: function (selectedTile) {
    let tile = this.tiles[selectedTile.index];

    if (tile.cover) {
      tile.cover = false;
    }
  }
},
  // instance hooks
  created: function () {
    this.fillMine();
    this.fillHint();
  } // created
}
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

.tile-mine {
  margin: 5px 5px;
  width: 30px;
  height: 30px;
}

.tile-flag {
  margin: 5px 5px;
  width: 30px;
  height: 30px;
}
</style>
