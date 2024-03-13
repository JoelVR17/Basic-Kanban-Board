<script setup>
import { reactive } from "vue";
import InputNew from "./InputNew.vue";

let boards = reactive([
  {
    id: crypto.randomUUID(),
    name: "Board 1",
    items: [
      {
        id: crypto.randomUUID(),
        title: "Test 1",
      },
      {
        id: crypto.randomUUID(),
        title: "Test 2",
      },
    ],
  },
  {
    id: crypto.randomUUID(),
    name: "Board 2",
    items: [
      {
        id: crypto.randomUUID(),
        title: "Test 3",
      },
      {
        id: crypto.randomUUID(),
        title: "Test 4",
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

function handleDeleteBoard(boardId) {

  const boardIndex = boards.findIndex((board) => board.id === boardId);

  if (boardIndex !== -1) {
    boards.splice(boardIndex, 1);
  }
}

function handleDeleteItem(boardId, itemId) {
  
  const board = boards.find((item) => item.id === boardId);
  const itemIndex = board.items.findIndex((item) => item.id === itemId);

  if (itemIndex !== -1) {
    board.items.splice(itemIndex, 1);
  }
}

function startDrag(evt, board, item) {
  evt.dataTransfer.setData(
    "text/plain",
    JSON.stringify({ boardId: board.id, itemId: item.id })
  );
}

function onDrop(evt, dest) {
  const { boardId, itemId } = JSON.parse(
    evt.dataTransfer.getData("text/plain")
  );

  const originBoard = boards.find((item) => item.id === boardId);
  const originItem = originBoard.items.find((item) => item.id === itemId);

  dest.items.push({ ...originItem });
  originBoard.items = originBoard.items.filter((item) => item !== originItem);
}
</script>

<template>
  <div class="mx-auto px-20 my-10">
    <h1 className="text-teal-700 font-black text-6xl capitalize my-5">
      Kanban Board
      <span className="text-slate-700">- For Projects</span>
    </h1>

    <nav>
      <ul>
        <li class="my-10">
          <a
            class="text-white bg-gradient-to-r from-teal-600 via-teal-700 to-teal-800 hover:bg-gradient-to-br focus:ring-4 focus:outline-none focus:ring-teal-300 dark:focus:ring-teal-800 font-medium rounded-lg text-sm px-5 py-2.5 text-center me-2"
            href="#"
            @click.prevent="handleNewBoard"
            >Create Board
          </a>
        </li>
      </ul>
    </nav>

    <div
      class="mx-auto p-8 overflow-x-auto bg-slate-100 rounded-2xl min-h-full"
    >
      <div class="flex justify-center">
        <div v-if="boards.length === 0">
          <p class="font-bold text-red-900 text-3xl bg-red-100 rounded-md px-8 py-3 shadow-xl">There aren't Boards</p>
        </div>
        <div
          class="flex-shrink-0 w-1/5 p-4"
          @drop="onDrop($event, board)"
          @dragover.prevent
          @dragenter.prevent
          v-for="board in boards"
          :key="board.id"
        >
          <div class="bg-white rounded shadow-xl p-5">
            <div class="flex justify-between">
              <h2 class="text-lg font-semibold mb-4">{{ board.name }}</h2>
              <a
                href="#"
                class="font-bold text-red-800 text-xl"
                @click.prevent="handleDeleteBoard(board.id)"
                >X</a
              >
            </div>
            <InputNew @onNewItem="(text) => handleNewItem(text, board)" />
            <div
              class="p-2 rounded item"
              draggable="true"
              @dragstart="startDrag($event, board, item)"
              v-for="item in board.items"
              :key="item.id"
            >
              <div class="shadow-md min-h-20 bg-slate-50 p-3 rounded">
                <div class="flex justify-between">
                  <h5 class="font-semibold">{{ item.title }}</h5>
                  <a
                    href="#"
                    class="font-bold text-red-800 text-sm"
                    @click.prevent="handleDeleteItem(board.id, item.id)"
                    >X</a
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
