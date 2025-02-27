<script setup lang="ts">
import { computed, ref, watch } from "vue"
import type { Game } from "../scripts/types"
import Item from "./Item.vue"

const games = ref<Game[]>([
  {
    id: 1,
    name: "The Last of Us Part II",
    description: "A game about survival",
    platform: "PlayStation",
    price: 65,
    quantity: 50,
  },
  {
    id: 2,
    name: "The Elder Scrolls V: Skyrim",
    description: "A game about dragons",
    platform: "PC",
    price: 53.49,
    quantity: 30,
  },
  {
    id: 3,
    name: "Cyberpunk 2077",
    description: "A game about the future",
    platform: "PlayStation",
    price: 59.99,
    quantity: 20,
  },
  {
    id: 4,
    name: "Fallout 4",
    description: "A game about the apocalypse",
    platform: "Xbox",
    price: 29.99,
    quantity: 10,
  },
  {
    id: 5,
    name: "The Witcher 3: Wild Hunt",
    description: "A game about monsters",
    platform: "PC",
    price: 55.99,
    quantity: 40,
  },
  {
    id: 6,
    name: "Outer Wilds",
    description: "A game about space",
    platform: "PC",
    price: 19.99,
    quantity: 15,
  },
  {
    id: 7,
    name: "Days Gone",
    description: "A game about zombies",
    platform: "PlayStation",
    price: 49.99,
    quantity: 25,
  },
  {
    id: 8,
    name: "The Legend of Zelda: Link's Awakening",
    description: "A game about adventure",
    platform: "Switch",
    price: 79.99,
    quantity: 5,
  },
])
const newGame = ref<string>("")
const newDescription = ref<string>("")
const newPrice = ref<number>(0)
const newQuantity = ref<number>(1)
const updatedName = ref<string>("")
const updatedDescription = ref<string>("")
const updatedPlatform = ref<string>("")
const updatedPrice = ref<number>(0)
const updatedQuantity = ref<number>(1)
const gameIdToUpdate = ref<number | null>(null)
const newPlatform = ref<string>("PC")
const searchQuery = ref<string>("")
const selectedGame = ref<Game | null>(null)
const quantityError = ref<string>("")
const nameError = ref<string>("")
const priceError = ref<string>("")
const descriptionError = ref<string>("")
const updatedQuantityError = ref<string>("")
const updatedNameError = ref<string>("")
const updatedPriceError = ref<string>("")
const updatedDescriptionError = ref<string>("")
const outOfStockErrors = ref<string[]>([])
const outOfStockGames = new Set<number>()

const addGame = () => {
  let hasError = false
  if (newGame.value.length < 2) {
    nameError.value = "Name must be at least 2 characters long"
    hasError = true
  } else {
    nameError.value = ""
  }

  if (newDescription.value === "") {
    descriptionError.value = "Description cannot be empty"
    hasError = true
  } else {
    nameError.value = ""
  }

  if (newPrice.value < 0) {
    priceError.value = "Price must be greater than or equal to 0"
    hasError = true
  } else {
    priceError.value = ""
  }

  if (newQuantity.value < 0) {
    quantityError.value = "Quantity must be greater than 0"
    hasError = true
  } else {
    quantityError.value = ""
  }

  if (hasError) {
    return
  }

  const newGameObj = {
    id: games.value.length + 1,
    name: newGame.value,
    description: newDescription.value,
    platform: newPlatform.value,
    price: newPrice.value,
    quantity: newQuantity.value,
  }

  games.value.push(newGameObj)

  if (newQuantity.value == 0) {
    outOfStockErrors.value.push(`The game "${newGame.value}" is out of stock`)
    outOfStockGames.add(newGameObj.id)
  }

  newGame.value = ""
  newDescription.value = ""
  newPlatform.value = "PC"
  newPrice.value = 0
  newQuantity.value = 1
}

const removeGame = (id: number) => {
  const game = games.value.find((game) => game.id === id)
  if (game) {
    outOfStockGames.delete(id)
    outOfStockErrors.value = outOfStockErrors.value.filter(
      (error) => !error.includes(`"${game.name}"`)
    )
    games.value = games.value.filter((game) => game.id !== id)
  }
}

const setGameToUpdate = (
  id: number,
  name: string,
  description: string,
  platform: string,
  price: number,
  quantity: number
) => {
  gameIdToUpdate.value = id
  updatedName.value = name
  updatedDescription.value = description
  updatedPlatform.value = platform
  updatedPrice.value = price
  updatedQuantity.value = quantity
}

const setGameToDuplicate = (
  name: string,
  description: string,
  platform: string,
  price: number
) => {
  newGame.value = name
  newDescription.value = description
  newPlatform.value = platform
  newPrice.value = price
}

const updateGame = () => {
  if (gameIdToUpdate.value !== null) {
    const game = games.value.find((game) => game.id === gameIdToUpdate.value)
    if (game) {
      let hasError = false
      if (updatedName.value.length < 2) {
        updatedNameError.value = "Name must be at least 2 characters long"
        hasError = true
      } else {
        updatedNameError.value = ""
      }

      if (updatedDescription.value === "") {
        updatedDescriptionError.value = "Description cannot be empty"
        hasError = true
      } else {
        updatedDescriptionError.value = ""
      }

      if (updatedPrice.value < 0) {
        updatedPriceError.value = "Price must be greater than or equal to 0"
        hasError = true
      } else {
        updatedPriceError.value = ""
      }

      if (updatedQuantity.value < 0) {
        updatedQuantityError.value = "Quantity must be greater than 0"
        hasError = true
      } else {
        updatedQuantityError.value = ""
      }

      if (hasError) {
        return
      }

      game.name = updatedName.value
      game.description = updatedDescription.value
      game.platform = updatedPlatform.value
      game.price = updatedPrice.value
      game.quantity = updatedQuantity.value
    }
    updatedName.value = ""
    updatedDescription.value = ""
    updatedPlatform.value = ""
    updatedPrice.value = 0
    gameIdToUpdate.value = null
    updatedQuantity.value = 1
  }
}

const sellGame = (id: number, quantity: number) => {
  const game = games.value.find((game) => game.id === id)
  if (game && game.quantity > 0) {
    game.quantity = quantity
  }
}

watch(
  games,
  (newGames) => {
    newGames.forEach((game) => {
      if (game.quantity === 0 && !outOfStockGames.has(game.id)) {
        if (
          !outOfStockErrors.value.includes(
            `The game "${game.name}" is out of stock`
          )
        ) {
          outOfStockErrors.value.push(`The game "${game.name}" is out of stock`)
        }
        outOfStockGames.add(game.id)
      } else if (game.quantity > 0 && outOfStockGames.has(game.id)) {
        outOfStockErrors.value = outOfStockErrors.value.filter(
          (error) => !error.includes(`"${game.name}"`)
        )
        outOfStockGames.delete(game.id)
      }
    })
  },
  { deep: true }
)

const filteredGames = computed(() => {
  if (!searchQuery.value) {
    return games.value
  }
  return games.value.filter((game) => {
    return game.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  })
})

const selectGame = (game: Game) => {
  selectedGame.value = game
}

const convertToCSV = (
  data: {
    id: any
    name: any
    description: any
    platform: any
    price: any
    quantity: any
  }[]
) => {
  const headers = [
    "ID",
    " Name",
    " Description",
    " Platform",
    " Price",
    " Quantity",
  ]
  const rows = data.map(
    (game: {
      id: any
      name: any
      description: any
      platform: any
      price: any
      quantity: any
    }) => [
      game.id,
      " " + game.name,
      " " + game.description,
      " " + game.platform,
      " " + game.price + "$",
      " " + game.quantity,
    ]
  )

  let csvContent = "data:text/csv;charset=utf-8,"
  csvContent += headers.join(",") + "\n"
  rows.forEach((row) => {
    csvContent += row.join(",") + "\n"
  })

  return encodeURI(csvContent)
}

const downloadCSV = () => {
  const csvContent = convertToCSV(games.value)
  const link = document.createElement("a")
  link.setAttribute("href", csvContent)
  link.setAttribute("download", "games.csv")
  document.body.appendChild(link)
  link.click()
  document.body.removeChild(link)
}
</script>

<template>
  <div class="container mt-5 p-3">
    <h2 class="mb-4">🎮 Brandon's game store</h2>
    <div class="row">
      <div class="card col-6 mb-3">
        <div class="card-header bg-success">Add a game</div>
        <input
          v-model="newGame"
          class="form-control mt-2"
          placeholder="Enter a new game"
        />
        <input
          v-model="newDescription"
          class="form-control mt-2"
          placeholder="Enter the game's description"
        />
        <select v-model="newPlatform" class="form-select mt-2">
          <option>PlayStation</option>
          <option>Xbox</option>
          <option>Switch</option>
          <option>PC</option>
        </select>
        <input
          v-model="newPrice"
          class="form-control mt-2"
          placeholder="Enter the price"
        />
        <input
          v-model="newQuantity"
          class="form-control mt-2"
          placeholder="Enter the quantity"
        />
        <button
          @click="addGame"
          class="btn btn-success mt-2"
          :disabled="!newGame"
        >
          ➕ Add Game
        </button>
        <div v-if="nameError" class="alert alert-danger mt-2">
          {{ nameError }}
        </div>
        <div v-if="priceError" class="alert alert-danger mt-2">
          {{ priceError }}
        </div>
        <div v-if="descriptionError" class="alert alert-danger mt-2">
          {{ descriptionError }}
        </div>
        <div v-if="quantityError" class="alert alert-danger mt-2">
          {{ quantityError }}
        </div>
      </div>
      <div class="card col-6 mb-3">
        <div class="card-header bg-warning">Update a game</div>
        <input
          v-model="updatedName"
          class="form-control mt-2"
          placeholder="Update the game's name"
        />
        <input
          v-model="updatedDescription"
          class="form-control mt-2"
          placeholder="Update the game's description"
        />
        <select v-model="updatedPlatform" class="form-select mt-2">
          <option>PlayStation</option>
          <option>Xbox</option>
          <option>Switch</option>
          <option>PC</option>
        </select>
        <input
          v-model="updatedPrice"
          class="form-control mt-2"
          placeholder="Enter the price"
        />
        <input
          v-model="updatedQuantity"
          class="form-control mt-2"
          placeholder="Enter the quantity"
        />
        <button
          @click="updateGame"
          class="btn btn-warning mt-2"
          :disabled="!updatedName"
        >
          ✏️ Update Game
        </button>
        <div v-if="updatedNameError" class="alert alert-danger mt-2">
          {{ updatedNameError }}
        </div>
        <div v-if="updatedPriceError" class="alert alert-danger mt-2">
          {{ updatedPriceError }}
        </div>
        <div v-if="updatedDescriptionError" class="alert alert-danger mt-2">
          {{ updatedDescriptionError }}
        </div>
        <div v-if="updatedQuantityError" class="alert alert-danger mt-2">
          {{ updatedQuantityError }}
        </div>
      </div>
    </div>
    <div>
      <h2>Inventory</h2>
      <input
        v-model="searchQuery"
        class="form-control mb-3"
        placeholder="Search for a game"
      />
      <div v-if="filteredGames.length === 0" class="alert alert-info">
        No games found
      </div>
      <div v-else>
        <div class="list-group">
          <Item
            v-for="game in filteredGames"
            :key="game.id"
            :game="game"
            :onRemove="removeGame"
            :onUpdate="setGameToUpdate"
            :onDuplicate="setGameToDuplicate"
            :onSell="sellGame"
            @click="selectGame(game)"
            class="list-group-item"
          />
        </div>
      </div>
    </div>
    <div v-if="selectedGame" class="card mt-4 selected-game-card">
      <h3>Selected Game</h3>
      <p><strong>Name:</strong> {{ selectedGame.name }}</p>
      <p><strong>Description:</strong> {{ selectedGame.description }}</p>
      <p><strong>Platform:</strong> {{ selectedGame.platform }}</p>
      <p><strong>Price:</strong> {{ selectedGame.price }} $</p>
      <p><strong>Quantity:</strong> {{ selectedGame.quantity }}</p>
    </div>
    <div v-if="outOfStockErrors.length" class="alert alert-danger mt-4">
      <ul>
        <li v-for="error in outOfStockErrors" :key="error">{{ error }}</li>
      </ul>
    </div>
    <button @click="downloadCSV" class="btn btn-primary mt-4">
      Export to CSV
    </button>
  </div>
</template>

<style scoped>
.selected-game-card {
  padding: 1rem;
  margin: 1rem auto;
  max-width: 400px;
}
</style>
