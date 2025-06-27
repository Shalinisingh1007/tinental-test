<script lang="ts">
  import { goto } from '$app/navigation';
  import '../app.css'; 

  function viewStudents(): void {
    goto('/students');
  }

  function viewProfessors(): void {
    goto('/professors');
  }
</script>



<main class="min-h-screen flex flex-col items-center justify-center gap-6 p-4">
  <h1 class="text-2xl font-bold text-gray-800">
    Benvenuto nell'Anagrafica Scuolastica
  </h1>

  <div class="flex gap-4">
    <button
      on:click={viewStudents}
      class="px-6 py-2 bg-blue-600 text-white rounded-lg shadow hover:bg-blue-700 transition"
    >
      Visualizza Studenti
    </button>

    <button
      on:click={viewProfessors}
      class="px-6 py-2 bg-green-600 text-white rounded-lg shadow hover:bg-green-700 transition"
    >
      Visualizza Professori
    </button>
  </div>
</main>
