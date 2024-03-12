<script setup>
import { reactive } from "vue";
import InputNew from "./InputNew.vue";

let boards = reactive([
  {
    id: crypto.randomUUID(),
    name: "Tablero 1",
    items: [
      {
        id: crypto.randomUUID(),
        title: "Feature de Archivos",
      },
      {
        id: crypto.randomUUID(),
        title: "Joel",
      },
    ],
  },
  {
    id: crypto.randomUUID(),
    name: "Tablero 2",
    items: [
      {
        id: crypto.randomUUID(),
        title: "Feature de Docs",
      },
      {
        id: crypto.randomUUID(),
        title: "Joel 2",
      },
    ],
  },
]);

function handleNewItem(text, board) {
  board.items.push({
    id: crypto.randomUUID(),
    title: text,
  });
}

function handleNewBoard(text, board) {
  const name = prompt("Name of the Board");

  if (!!name) {
    boards.push({
      id: crypto.randomUUID(),
      name: name,
      items: [],
    });
  }
}

function startDrag(evt, board, item) {
    evt.dataTransfer.setData('text/plain', JSON.stringify({boardId: board.id, itemId: item.id}))
}

function onDrop(evt, dest) {
    const {boardId, itemId} = JSON.parse(evt.dataTransfer.getData('text/plain'))

    const originBoard = boards.find((item) => item.id === boardId)
    const originItem = originBoard.items.find((item) => item.id === itemId)

    dest.items.push({...originItem})
    originBoard.items = originBoard.items.filter((item) => item !== originItem)
}

</script>

<template>
  <nav>
    <ul>
      <li><a href="#" @click.prevent="handleNewBoard">Create Board</a></li>
    </ul>
  </nav>

  <div class="boards-container">
    <div class="boards">
      <div
        class="board"
        @drop="onDrop($event, board)"
        @dragover.prevent
        @dragenter.prevent
        v-for="board in boards"
        :key="board.id"
      >
        <div>{{ board.name }}</div>
        <InputNew @onNewItem="(text) => handleNewItem(text, board)" />
        <div class="items">
          <div class="item" draggable="true" @dragstart="startDrag($event, board, item)" v-for="item in board.items" :key="item.id">
            {{ item.title }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.boards {
  display: flex;
  gap: 10px;
}

.board {
  border: 1px solid #ccc;
  border-radius: 10px;
  padding: 10px;
}

.item {
  padding: 5px;
  box-sizing: border-box;
}
</style>
