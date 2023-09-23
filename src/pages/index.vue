<script setup lang="ts" generic="T extends any, O extends any">
interface BlockState {
  x: number
  y: number
  mine?: boolean
  revealed?: boolean
  flagger?: boolean
  adjacentMine: number
}

const HEIGHT = 10
const WIDTH = 10

const state = ref(Array.from({ length: HEIGHT },
  (_, y) => (
    Array.from({ length: WIDTH },
      (_, x): BlockState => ({
        x,
        y,
        adjacentMine : 0,
        revealed: false,
      }),
    )
  )),
)

// 生成炸弹
function generateMines() {
  for(const row of state.value){
    for(const block of row)
      block.mine = Math.random() < 0.1
  }
}

// 方向
const direction = [
  [-1,1],
  [0,1],
  [1,1],
  [-1,0],
  [1,0],
  [-1,-1],
  [0,-1],
  [1,-1],
]

// 更新数字
function updateNumber() {
  state.value.forEach((row,y)=>{
    row.forEach((block,x)=>{
      if(block.mine)
        return
      direction.forEach(([dx,dy])=>{
        const xx = x + dx
        const yy = y + dy
        if(xx < 0|| xx >= WIDTH || yy < 0 || yy >= HEIGHT)
          return
        if(state.value[yy][xx].mine)
          block.adjacentMine++
      })
    })
  })
}

// 点击
function onClick(block: BlockState) {
  settlement(block)
  if(block.revealed===false)
    block.revealed = true
}

// 调节样式
function getBlockClass(block: BlockState) {
  if(!block.revealed)
    return 'bg-gray-500/20'
  else
    return 'bg-gray-500/3'
}

// 结算
function settlement(block: BlockState) {
  if(block.mine)
    // eslint-disable-next-line no-alert
    alert('你输了')
}

generateMines()
updateNumber()
</script>

<template>
  <div>
    <h2>Minesweepr</h2>
    <main m-t-5>
      <div
        v-for="row,y in state"
        :key="y"
        flex
        items-center
        justify-center
      >
        <button
          v-for="item,x of row"
          :key="x"
          m-0.5 h-10 w-10
          flex
          items-center
          justify-center
          border="~ gray-500/10"
          hover="bg-gray-500/30"
          :class="getBlockClass(item)"
          @click="onClick(item)"
        >
          <div v-show="!item.revealed">
            <div v-if="item.mine" i-mdi:mine/>
            <div v-else >
              {{item.adjacentMine }}
            </div>
          </div>
        </button>
      </div>
    </main>
  </div>
</template>
