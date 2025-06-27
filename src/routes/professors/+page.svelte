<script lang="ts">
  import { goto } from '$app/navigation';

  let isLoading = false;

  type Professor = {
    id:number;
    nome: string;
    cognome: string;
    materia: string;
  };

  let professors: Professor[] = [];

  // Simulated DB
  let mockDBProfessors: Professor[] = [
    { id:1, nome: "Giulia", cognome: "Ferrari", materia: "Matematica" },
    { id:2, nome: "Marco", cognome: "Esposito", materia: "Fisica" },
    { id:3, nome: "Sara", cognome: "Russo", materia: "Italiano" }
  ];

  // Form fields
  let nome = '';
  let cognome = '';
  let materia = '';
  let editingId: number | null = null; // track editing by id

  // Simulated API calls
  function getProfessorsFromDB(): Promise<Professor[]> {
    return new Promise(resolve => {
      setTimeout(() => resolve([...mockDBProfessors]), 300);
    });
  }

  function saveProfessorToDB(prof: Professor): Promise<void> {
    return new Promise(resolve => {
      setTimeout(() => {
        if (prof.id === 0) {
          // Add new professor - id = length + 1
          const newId = mockDBProfessors.length > 0 
          ? Math.max(...mockDBProfessors.map(p => p.id)) + 1 
          : 1;
          prof.id = newId
          mockDBProfessors.push(prof);
        } else {
          // Update existing professor by id
          const idx = mockDBProfessors.findIndex(p => p.id === prof.id);
          if (idx !== -1) mockDBProfessors[idx] = prof;
        }
        resolve();
      }, 300);
    });
  }

  function deleteProfessorFromDB(id: number): Promise<void> {
    return new Promise(resolve => {
      setTimeout(() => {
        mockDBProfessors = mockDBProfessors.filter(s => s.id !== id);
        resolve();
      }, 300);
    });
  }

  async function fetchProfessors(): Promise<void> {
    isLoading = true;
    try{
      professors = await getProfessorsFromDB();
    }finally{
      isLoading = false;
    }
  }

  async function addOrUpdateProfessor(): Promise<void> {
    if (!nome || !cognome || !materia) {
      alert("Per favore, compila tutti i campi.");
      return;
    }

    const professor: Professor = 
    { 
      id: editingId ?? 0,  // 0 means new prof
      nome, 
      cognome, 
      materia 
    };

    isLoading = true;
    try{
        await saveProfessorToDB(professor);
        await fetchProfessors();
        resetForm();
      }finally{
        isLoading = false;
      }
    
  }

  async function deleteProfessor(id: number): Promise<void> {
    if (confirm("Vuoi davvero eliminare questo professore?")) {

      isLoading = true;
      try{
          await deleteProfessorFromDB(id);
          await fetchProfessors();
          if (editingId === id) resetForm();
        }finally{
          isLoading = false;
        }
     
    }
  }

  function editProfessor(id: number): void {
    const prof = professors.find(p => p.id === id);
    if (!prof) return;
    nome = prof.nome;
    cognome = prof.cognome;
    materia = prof.materia;
    editingId = prof.id;
  }

  function resetForm(): void {
    nome = '';
    cognome = '';
    materia = '';
    editingId = null;
  }

  function goBack(): void {
    goto('/');
  }

  // Initial load
  fetchProfessors();
</script>

<main class="p-6">
<!-- Header -->
  <div class="relative rounded-t-xl bg-blue-600 text-white text-center py-4 shadow-md mb-6">
    <h1 class="text-2xl font-bold">Elenco Professori</h1>
    <button
      on:click={goBack}
      class="absolute top-1/2 left-4 -translate-y-1/2 text-white hover:text-gray-200 transition"
      aria-label="Go Back"
      disabled={isLoading}
    >
      <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2" disabled={isLoading}>
        <path stroke-linecap="round" stroke-linejoin="round" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
      </svg>
    </button>
  </div>

  {#if isLoading}
    <div class="flex items-center justify-center py-6 space-x-2">
      <svg class="animate-spin h-6 w-6 text-blue-600" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
        <path class="opacity-75" fill="currentColor"
          d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z"></path>
      </svg>
      <span class="text-blue-600 font-medium animate-pulse">Caricamento in corso...</span>
    </div>
  {/if}

<!-- Professor Table -->
{#if !isLoading}
  <table class="min-w-full border border-gray-300 rounded-b-xl overflow-hidden shadow mb-8">
    <thead class="bg-gray-100 text-gray-700">
      <tr>
        <th class="px-4 py-3 border-b text-left">Nome</th>
        <th class="px-4 py-3 border-b text-left">Cognome</th>
        <th class="px-4 py-3 border-b text-left">Materia</th>
        <th class="px-4 py-3 border-b text-left">Id</th>
        <th class="px-4 py-3 border-b text-left">Azioni</th>
      </tr>
    </thead>
    <tbody>
      {#each professors as professor, index}
        <tr class="hover:bg-blue-50 even:bg-white odd:bg-gray-50 transition-colors">
          <td class="px-4 py-2 border-b">{professor.nome}</td>
          <td class="px-4 py-2 border-b">{professor.cognome}</td>
          <td class="px-4 py-2 border-b">{professor.materia}</td>
          <td class="px-4 py-2 border-b">{professor.id}</td>
          <td class="px-4 py-2 border-b space-x-2">
            <button class="text-sm text-blue-600 hover:underline" on:click={() => editProfessor(professor.id)} disabled={isLoading}>
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M15.232 5.232l3.536 3.536M16.5 3.75a2.121 2.121 0 013 3L7 19.25H4.5v-2.5L16.5 3.75z" />
                </svg>
            </button>
            <button class="text-sm text-red-600 hover:underline" on:click={() => deleteProfessor(professor.id)} disabled={isLoading}>
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                <path stroke-linecap="round" stroke-linejoin="round" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5-4h4a1 1 0 011 1v1H9V4a1 1 0 011-1z" />
              </svg>
            </button>
          </td>
        </tr>
      {/each}
    </tbody>
  </table>
{/if}

<!-- Form -->
  <div class="bg-white rounded-xl shadow-md p-6 max-w-2xl mx-auto border border-gray-200">
    <h2 class="text-lg font-semibold text-gray-800 mb-4 text-center">
      {editingId === null ? "Aggiungi Nuovo Professore" : "Modifica Professore"}
    </h2>
    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-4">
      <input type="text" bind:value={nome} placeholder="Nome" class="border p-2 rounded w-full" />
      <input type="text" bind:value={cognome} placeholder="Cognome" class="border p-2 rounded w-full" />
      <input type="text" bind:value={materia} placeholder="Materia (es. Matematica)" class="border p-2 rounded w-full" />
    </div>
    <div class="flex justify-center gap-4">
      <button
        on:click={addOrUpdateProfessor}
        class="bg-blue-600 text-white px-6 py-2 rounded hover:bg-blue-700 transition"
        disabled={isLoading}
      >
        {editingId === null ? "Aggiungi" : "Aggiorna"}
      </button>
      {#if editingId !== null}
        <button
          on:click={resetForm}
          class="bg-blue-600 text-white px-6 py-2 rounded hover:bg-blue-700 transition"
          disabled={isLoading}
        >
          Annulla
        </button>
      {/if}
    </div>
  </div>
</main>

