<script setup>
import { ref } from 'vue'
const names = ref([])
const name = ref("Tage")
const startGame = ref(false)
const currentPlayer = ref(0)
const points = ref([])
const dice = ref([])
const currentPoints = ref(0)
const saved = ref([])
const rollSaved = ref([])
const images = ref(["", "https://www.svgrepo.com/show/344723/dice-1.svg", "https://www.svgrepo.com/show/320118/inverted-dice-2.svg", "https://www.svgrepo.com/show/320121/inverted-dice-3.svg", "https://www.svgrepo.com/show/320120/inverted-dice-4.svg", "https://www.svgrepo.com/show/344726/dice-5.svg", "https://www.svgrepo.com/show/344728/dice-6.svg"])

for(let d in rollSaved.value){
    saved.value.push(rollSaved.value[d])
  }
  rollSaved.value = []
  for(let i = 0; i<6-saved.value.length; i++){
    dice.value[i] = {val: getRandomInt(5) + 1, id: i}
  }

const addName = () => {
  names.value.push(name.value)
  points.value.push(0)
  name.value = ""
}
const start = () => {
  if (names.value != []){
    startGame.value = !startGame.value
}}

function getRandomInt(max) {
  return Math.floor(Math.random() * max);
}

const saveDice = () => {
  let numDice = [0,0,0,0,0,0]
  let addPoints = 0
  let notThree = false
  for(let d in rollSaved.value){
    numDice[rollSaved.value[d].val-1] += 1
  }
  console.log(numDice)
  for (let i in numDice){
    
    if (i != 0 && i != 4 && numDice[i] % 3 == 0){
      // console.log(i, (numDice[i]/3), (parseInt(i)+1), 100, (numDice[i]/3) * (i+1) * 100)
      addPoints += (numDice[i]/3) * (parseInt(i)+1) * 100
  }
  else if (i != 0 && i != 4) notThree = true
  
}
  addPoints += Math.floor(numDice[0]/3)*1000
  addPoints += Math.floor(numDice[4]/3)*500
  addPoints += (numDice[0]%3)*100
  addPoints += (numDice[4]%3)*50

  let stair = true
  for(let i in numDice){
    if (numDice[i] == 0) stair = false
  }

  if (stair) addPoints += 750
  else if (notThree) return false

  currentPoints.value += addPoints

  if (0 != addPoints) return true
  else return false
  
}

const roll = () => {
  
  if (saveDice()){
  for(let d in rollSaved.value){
    saved.value.push(rollSaved.value[d])
  }
  rollSaved.value = []
  if (saved.value.length == 6){
    saved.value = []
  }
  for(let i = 0; i<6-saved.value.length; i++){
    dice.value[i] = {val: getRandomInt(5) + 1, id: i}
  }
}
}

const finish = () => {
  if(!saveDice()) currentPoints.value = 0
  rollSaved.value = []
  points.value[currentPlayer.value] += currentPoints.value
  currentPlayer.value = (currentPlayer.value + 1) % names.value.length
  currentPoints.value = 0
  saved.value = []
  for(let i = 0; i<6-saved.value.length; i++){
    dice.value[i] = {val: getRandomInt(5) + 1, id: i}
  }
}

const save = (i) => {
  let d = dice.value.splice(i,1)[0]
  rollSaved.value = [...rollSaved.value, d]
}

const unSave = (i) => {
  let d = rollSaved.value.splice(i,1)[0]
  dice.value = [...dice.value, d]
}



</script>

<template>
  <div class="m-10" v-if="!startGame">
    <div class="font-bold text-3xl">
    Dice Game
  </div>
    <div class = "flex flex-col">
      <input type="text" v-model="name" class = "w-40 h-10 bg-gray-100 border"/>
      <button class = "rounded-b-lg w-40 h-10 bg-gray-100 border" @click="addName()">Add Person</button>
    </div>
    
    <ul>
      <li v-for="n in names" :key="names.indexOf(n)">
      {{ n }}
      </li>
    </ul>
    <button class = "rounded-b-lg w-40 h-10 bg-gray-100 border" @click="start()">Start Game</button>
  </div>
  <div class="m-10" v-if="startGame">
    <div class="font-bold text-3xl">
      {{ names[currentPlayer] }} {{points[currentPlayer]}}
    </div>
    <button class = "rounded-b-lg w-40 h-10 bg-gray-100 border" @click="roll()">Roll</button>
    <button class = "rounded-b-lg w-40 h-10 bg-gray-100 border" @click="finish()">Save or continue</button>
    <div class="flex flex-col divide-y-2 divide-gray-500">
    <div class = "flex gap-5 h-40 items-center text-center py-5">
      
      <button v-for="d in dice" class="bg-cover bg-center w-20 h-20" :style="`background-image: url(${images[d.val]})`" :key=d.id @click="save(dice.indexOf(d))"></button>
    </div>
    <div class = "flex gap-5 h-40 items-center text-center py-5">
      <button v-for="d in rollSaved" class="bg-cover bg-center w-20 h-20" :style="`background-image: url(${images[d.val]})`" :key=d.id @click="unSave(rollSaved.indexOf(d))"></button>
    </div>
    <div class = "flex gap-5 h-40 items-center text-center py-5">
      <div v-for="d in saved" class="bg-cover bg-center w-20 h-20" :style="`background-image: url(${images[d.val]})`" :key=d.id></div> <div>Points: {{ currentPoints }}</div>
    </div>
  </div>
    
  </div>

</template>

