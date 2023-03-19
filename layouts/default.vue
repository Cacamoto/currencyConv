<template>
  <div
    class="absolute flex flex-col items-center justify-center bg-neutral-800 w-screen h-screen overflow-hidden"
  >
    <div
      class="-top-16 -right-16 absolute h-[20rem] w-[20rem] bg-gradient-to-r from-pink-500 via-red-500 to-yellow-500 rounded-full opacity-70 blur-xl"
    />
    <div
      class="top-[60vh] -left-16 absolute h-[20rem] w-[20rem] bg-gradient-to-r from-green-300 via-blue-500 to-purple-600 rounded-full opacity-60 blur-md"
    />
    <slot />
  </div>
</template>
