<script lang="ts">
  import { goto } from '$app/navigation';
 

  let isLoading = false;

  type Student = {
    id:number
    nome: string;
    cognome: string;
    eta: number;
    classe: string;
  };

  let students: Student[] = [];

  // Form data
  let nome = '';
  let cognome = '';
  let eta = '';
  let classe = '';
  let editingId: number | null = null; // track editing by id

  // Simulate DB
  let mockDB: Student[] = [
    { id:1, nome: "Luca", cognome: "Rossi", eta: 8, classe: "3A" },
    { id:2, nome: "Maria", cognome: "Bianchi", eta: 9, classe: "4B" },
    { id:3, nome: "Giovanni", cognome: "Verdi", eta: 7, classe: "2C" }
  ];

  // Simulate API: fetch
  function getStudentsFromDB(): Promise<Student[]> {
    return new Promise(resolve => {
      setTimeout(() => {
        resolve([...mockDB]);
      }, 300); // simulate delay
    });
  }

  // Simulate API: add or update
function saveStudentToDB(student: Student): Promise<void> {
    return new Promise(resolve => {
      setTimeout(() => {
        if (student.id === 0) {
          // Add new student: assign new id
          const newId = mockDB.length > 0 
          ? Math.max(...mockDB.map(p => p.id)) + 1 
          : 1;
          student.id = newId;
          mockDB.push(student);
        } else {
          // Update existing student by id
          const index = mockDB.findIndex(s => s.id === student.id);
          if (index !== -1) {
            mockDB[index] = student;
          }
        }
        resolve();
      }, 300);
    });
  }

  // Simulate API: delete
  function deleteStudentFromDB(id: number): Promise<void> {
    return new Promise(resolve => {
      setTimeout(() => {
        mockDB = mockDB.filter(s => s.id !== id);
        resolve();
      }, 300);
    });
  }

  // Main operation: save
  async function addOrUpdateStudent(): Promise<void> {
    if (!nome || !cognome || !eta || !classe) {
      alert("Per favore, compila tutti i campi.");
      return;
    }

    const student: Student = {
      id: editingId ?? 0,  // 0 means new student
      nome,
      cognome,
      eta: parseInt(eta),
      classe
    };

    isLoading = true;
    try {
      await saveStudentToDB(student);
      await fetchStudents();
      resetForm();
    } finally {
      isLoading = false;
    }
  }

 

  async function deleteStudent(id: number): Promise<void> {
    if (confirm("Sei sicuro di voler eliminare questo studente?")) {
      isLoading = true;
      try {
        await deleteStudentFromDB(id);
        await fetchStudents();
        if (editingId === id) resetForm();
      } finally {
        isLoading = false;
      }
    }
  }

  function editStudent(id: number): void {
    const student = students.find(s => s.id === id);
    if (!student) return;

    nome = student.nome;
    cognome = student.cognome;
    eta = student.eta.toString();
    classe = student.classe;
    editingId = student.id;
  }

  function resetForm(): void 
  {
    nome = "";
    cognome = "";
    eta = "";
    classe = "";
    editingId = null;
  }

  async function fetchStudents(): Promise<void> {
    isLoading = true;
    try {
      students = await getStudentsFromDB();
    } finally {
      isLoading = false;
    }
  }

  function goBack(): void {
    goto('/');
  }

  // Initial load
  fetchStudents();
</script>


<main class="p-6">
  <!-- Header -->
  <div class="relative rounded-t-xl bg-blue-600 text-white text-center py-4 shadow-md mb-6">
    <h1 class="text-2xl font-bold">Elenco Studenti</h1>
    <button
    on:click={goBack}
    class="absolute top-1/2 left-4 -translate-y-1/2 text-white hover:text-gray-200 transition"
    aria-label="Go Back"
    disabled={isLoading}
    >
    <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
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

  <!-- Student Table -->
  {#if !isLoading}
  <table class="min-w-full border border-gray-300 rounded-b-xl overflow-hidden shadow mb-8">
    <thead class="bg-gray-100 text-gray-700">
      <tr>
        <th class="px-4 py-3 border-b text-left">Nome</th>
        <th class="px-4 py-3 border-b text-left">Cognome</th>
        <th class="px-4 py-3 border-b text-left">Età</th>
        <th class="px-4 py-3 border-b text-left">Classe</th>
        <th class="px-4 py-3 border-b text-left">Id</th>
        <th class="px-4 py-3 border-b text-left">Azioni</th>
      </tr>
    </thead>
    <tbody>
      {#each students as student, index}
        <tr class="hover:bg-blue-50 even:bg-white odd:bg-gray-50 transition-colors">
          <td class="px-4 py-2 border-b">{student.nome}</td>
          <td class="px-4 py-2 border-b">{student.cognome}</td>
          <td class="px-4 py-2 border-b">{student.eta}</td>
          <td class="px-4 py-2 border-b">{student.classe}</td>
          <td class="px-4 py-2 border-b">{student.id}</td>
          <td class="px-4 py-2 border-b space-x-2">
            <button class="text-sm text-blue-600 hover:underline" on:click={() => editStudent(student.id)} disabled={isLoading}>
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M15.232 5.232l3.536 3.536M16.5 3.75a2.121 2.121 0 013 3L7 19.25H4.5v-2.5L16.5 3.75z" />
              </svg>
            </button>
            <button class="text-sm text-red-600 hover:underline" on:click={() => deleteStudent(student.id)} disabled={isLoading}>
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
      {editingId === null ? "Aggiungi Nuovo Studente" : "Modifica Studente"}
    </h2>
    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-4">
      <input type="text" bind:value={nome} placeholder="Nome" class="border p-2 rounded w-full" />
      <input type="text" bind:value={cognome} placeholder="Cognome" class="border p-2 rounded w-full" />
      <input type="number" bind:value={eta} placeholder="Età" class="border p-2 rounded w-full" />
      <input type="text" bind:value={classe} placeholder="Classe" class="border p-2 rounded w-full" />
    </div>
    <div class="flex justify-center gap-4">
      <button
        on:click={addOrUpdateStudent}
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


