<script setup lang="ts">
import { useTableStore } from "@/stores/table";
import { useCrazy8RenderStore } from "@/stores/crazy8Render";
import type { Crazy8Table } from "@/models/table/crazy8Table";
import Crazy8Player from "../components/player/Crazy8Player.vue";
import GameRound from "@/components/GameRound.vue";
import GameCard from "@/components/GameCard.vue";
import Crazy8EndResult from "@/components/results/Crazy8EndResult.vue";
import Crazy8SelectSuit from "@/components/Crazy8SelectSuit.vue";
import router from "@/router";

const table = useTableStore().table as Crazy8Table;
const render = useCrazy8RenderStore();

const draw = () => {
  const player = table.getTurnPlayer();
  if (render.renderAction == true && player.type == "user") {
    player.drawCard(table.deck.drawOne());
  }
};

const stackDeckStyle = (index: number) => {
  if (index != 0) return "xl:-ml-12 sm:-ml-9 -ml-8";
  else return "xl:ml-12 lg:ml-9 ml-8";
};

render.renderTable(table);

// ブラウザバックでHome
addEventListener("popstate", () => {
  router.push("/");
  location.reload();
});
</script>
<template>
  <div
    id="playersWrap"
    class="flex justify-between flex-col h-full w-full m-auto z-10"
  >
    <Crazy8Player :index="0" class="h-1/3 flex flex-col" />
    <div class="flex justify-between items-center h-1/3">
      <Crazy8Player
        :index="3"
        class="flex items-center lg:justify-around justify-between w-1/3"
      />

      <div class="flex relative">
        <Crazy8SelectSuit v-if="render.renderSelectSuit" />
        <GameCard
          v-if="table.cardPlaceArr.length != 0"
          :card="table.peekCardPlaceArr()"
          :isHide="false"
          :isShadow="false"
          :rotate="{ isRotate: false, class: '' }"
        />
        <TransitionGroup
          name="deck"
          tag="div"
          v-if="table.deck.deck.length != 0"
          class="flex"
        >
          <GameCard
            v-for="(card, index) of table.deck.deck"
            :key="index"
            @click="draw()"
            :card="card"
            :isHide="true"
            :isShadow="false"
            :rotate="{ isRotate: false, class: '' }"
            :class="stackDeckStyle(index)"
            class="sm:mx-0 mx-0"
          />
        </TransitionGroup>
      </div>

      <Crazy8Player
        :index="1"
        class="flex items-center flex-row-reverse lg:justify-around justify-between w-1/3"
      />
    </div>
    <Crazy8Player :index="2" class="h-1/3" />
  </div>

  <GameRound class="round" />

  <Transition name="fade">
    <Crazy8EndResult v-if="render.renderEndResult == true" />
  </Transition>
</template>
<style scoped>
.round {
  position: absolute;
  top: 90%;
  left: 90%;
  transform: translate(-90%, -90%);
}
@media (max-width: 640px) {
  .round {
    top: 95%;
    left: 90%;
    transform: translate(-90%, -95%);
  }
}
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.deck-move,
.deck-enter-active,
.deck-leave-active {
  transition: all 0.5s ease;
}
.deck-enter-from,
.deck-leave-to {
  opacity: 0;
  transform: translateX(30px);
}
.deck-leave-active {
  position: absolute;
}
</style>
