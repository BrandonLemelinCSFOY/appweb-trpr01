<script setup lang="ts">
import { computed } from "vue"
import { type Game } from "../scripts/types"

const props = defineProps<{
  game: Game
  onRemove: (id: number) => void
  onUpdate: (
    id: number,
    name: string,
    description: string,
    platform: string,
    price: number,
    quantity: number
  ) => void
  onDuplicate: (
    name: string,
    description: string,
    platform: string,
    price: number
  ) => void
  onSell: (id: number, quantity: number) => void
}>()

const handleRemove = () => {
  props.onRemove(props.game.id)
}

const handleUpdate = () => {
  props.onUpdate(
    props.game.id,
    props.game.name,
    props.game.description,
    props.game.platform,
    props.game.price,
    props.game.quantity
  )
}

const handleDuplicate = () => {
  props.onDuplicate(
    props.game.name,
    props.game.description,
    props.game.platform,
    props.game.price
  )
}

const handleSell = () => {
  if (props.game.quantity > 0) {
    props.onSell(props.game.id, props.game.quantity - 1)
  }
}

const badgeClass = computed(() => {
  switch (props.game.platform) {
    case "PlayStation":
      return "text-bg-primary"
    case "Xbox":
      return "text-bg-success"
    case "Switch":
      return "text-bg-danger"
    case "PC":
      return "text-bg-warning"
    default:
      return "text-bg-secondary"
  }
})

const quantityBadgeClass = computed(() => {
  if (props.game.quantity > 10) {
    return "text-bg-success"
  } else if (props.game.quantity <= 10 && props.game.quantity > 0) {
    return "text-bg-warning"
  } else if (props.game.quantity <= 0) {
    return "text-bg-danger"
  } else {
    return "text-bg-secondary"
  }
})
</script>
<template>
  <div>
    <div class="d-flex justify-content-between align-items-center">
      <div class="me-5">
        <span class="me-3">{{ game.name }}</span>
        <span class="badge rounded-pill text-bg-info me-3"
          >{{ game.price }} $</span
        >
        <span :class="['badge', quantityBadgeClass, 'rounded-pill', 'me-3']"
          >{{ game.quantity }} in stock</span
        >
        <span :class="['badge', badgeClass, 'rounded-pill']">{{
          game.platform
        }}</span>
      </div>
      <div>
        <button @click="handleUpdate" class="btn btn-warning btn-sm me-2">
          ✏️ Update
        </button>
        <button @click="handleRemove" class="btn btn-danger btn-sm">
          🗑️ Remove
        </button>
        <button @click="handleDuplicate" class="btn btn-info btn-sm">
          🔁 Duplicate
        </button>
        <button @click="handleSell" class="btn btn-secondary btn-sm">
          💰 Sell
        </button>
      </div>
    </div>
  </div>
</template>

<style></style>
