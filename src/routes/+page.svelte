<script lang="ts">
  import { goto } from '$app/navigation';
 

  function viewStudents(): void {
    goto('/students');
  }

  function viewProfessors(): void {
    goto('/professors');
  }
</script>



<main class="min-h-screen flex flex-col items-stretch justify-start gap-12 p-6">
  <h1 class="w-full text-4xl font-extrabold text-white text-center px-6 py-4 bg-blue-700 rounded-xl shadow-lg tracking-tight mb-12">
    Anagrafica Scolastica
  </h1>

  <div class="flex flex-col md:flex-row gap-6 justify-center items-center">
    <!-- Students Button -->
    <button class="flex flex-col items-center flex-1 max-w-md px-6 py-4 bg-blue-100 text-blue-900 rounded-xl hover:bg-blue-200 transition-shadow shadow-md" on:click={viewStudents}>
      <svg class="w-8 h-8 text-blue-600 mr-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" d="M12 14l9-5-9-5-9 5 9 5z" />
        <path stroke-linecap="round" stroke-linejoin="round" d="M12 14l6.16-3.422A12.083 12.083 0 0112 21a12.083 12.083 0 01-6.16-10.422L12 14z" />
      </svg>
      <div class="flex flex-col items-center">
        <div class="text-xl font-semibold">Studenti</div>
        <div class="text-sm text-blue-700">Gestisci i dati degli studenti</div>
      </div>
    </button>

    <!-- Professors Button -->
    <button class="flex flex-col items-center flex-1 max-w-md px-6 py-4 bg-green-100 text-green-900 rounded-xl hover:bg-green-200 transition-shadow shadow-md" on:click={viewProfessors}>
      <svg class="w-8 h-8 text-green-600 mr-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" d="M12 14c4.418 0 8-1.79 8-4s-3.582-4-8-4-8 1.79-8 4 3.582 4 8 4z" />
        <path stroke-linecap="round" stroke-linejoin="round" d="M4 14v2c0 2.21 3.582 4 8 4s8-1.79 8-4v-2" />
      </svg>
      <div class="flex flex-col items-center">
        <div class="text-xl font-semibold">Professori</div>
        <div class="text-sm text-green-700">Gestisci le informazioni dei professori</div>
      </div>
    </button>
  </div>
</main>

