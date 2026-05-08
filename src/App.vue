<script setup>
import { ref, onMounted, computed } from 'vue'

// --- DATA ---
const patients = ref([])
const newPatient = ref({ 
  name: '', 
  age: '', 
  email: '', 
  symptom: '', 
  disease: '' 
})
const isDarkMode = ref(false)
const search = ref('')

// --- METHODS ---
const addPatient = () => {
  if (newPatient.value.name && newPatient.value.email && newPatient.value.symptom) {
    patients.value.push({
      id: Date.now(),
      ...newPatient.value,
      date: new Date().toLocaleDateString()
    })
    saveData()
    // Reset form
    newPatient.value = { name: '', age: '', email: '', symptom: '', disease: '' }
  } else {
    alert("Please fill in Name, Email and select a Symptom!")
  }
}

const deletePatient = (id) => {
  patients.value = patients.value.filter(p => p.id !== id)
  saveData()
}

const saveData = () => {
  localStorage.setItem('clinic-data', JSON.stringify(patients.value))
}

const toggleTheme = () => {
  isDarkMode.value = !isDarkMode.value
}

// --- COMPUTED ---
const filteredPatients = computed(() => {
  return patients.value.filter(p => 
    p.name.toLowerCase().includes(search.value.toLowerCase()) ||
    p.email.toLowerCase().includes(search.value.toLowerCase())
  )
})

onMounted(() => {
  const saved = localStorage.getItem('clinic-data')
  if (saved) patients.value = JSON.parse(saved)
})
</script>

<template>
  <div :class="['app-container', isDarkMode ? 'dark' : 'light']">
    <aside class="sidebar">
      <h2>CLINIC MS</h2>
      <nav>
        <ul>
          <li class="active">🏠 Dashboard</li>
          <li>👥 Patients</li>
          <li>📅 Appointments</li>
        </ul>
      </nav>
      
      <div class="useful-links">
        <p>Helpful Resource:</p>
        <a href="https://www.who.int" target="_blank" class="sidebar-link">🌐 WHO Website</a>
      </div>

      <button @click="toggleTheme" class="theme-btn">
        {{ isDarkMode ? '☀️ Light Mode' : '🌙 Dark Mode' }}
      </button>
    </aside>

    <main class="main-content">
      <header>
        <h1>Patient Management</h1>
        <input v-model="search" type="text" placeholder="Search by name or email..." class="search-bar" />
      </header>

      <section class="form-section">
        <h3>Register New Patient</h3>
        <div class="form-container">
          <div class="input-row">
            <input v-model="newPatient.name" placeholder="Full Name" />
            <input v-model="newPatient.age" type="number" placeholder="Age" />
            <input v-model="newPatient.email" type="email" placeholder="Email Address" />
          </div>

          <div class="symptoms-group">
            <p>Main Symptom:</p>
            <label><input type="radio" v-model="newPatient.symptom" value="Fever" /> Fever</label>
            <label><input type="radio" v-model="newPatient.symptom" value="Cough" /> Cough</label>
            <label><input type="radio" v-model="newPatient.symptom" value="Headache" /> Headache</label>
            <label><input type="radio" v-model="newPatient.symptom" value="Other" /> Other</label>
          </div>

          <div class="input-row">
            <input v-model="newPatient.disease" placeholder="Diagnosis / Notes" />
            <button @click="addPatient" class="add-btn">Add Patient</button>
          </div>
        </div>
      </section>

      <section class="list-section">
        <h3>Patient List ({{ filteredPatients.length }})</h3>
        <div class="table-wrapper">
          <table>
            <thead>
              <tr>
                <th>Name</th>
                <th>Email</th>
                <th>Symptom</th>
                <th>Date</th>
                <th>Action</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="patient in filteredPatients" :key="patient.id">
                <td>{{ patient.name }} ({{ patient.age }})</td>
                <td><a :href="'mailto:' + patient.email" class="email-link">{{ patient.email }}</a></td>
                <td><span class="badge">{{ patient.symptom }}</span></td>
                <td>{{ patient.date }}</td>
                <td><button @click="deletePatient(patient.id)" class="del-btn">Delete</button></td>
              </tr>
            </tbody>
          </table>
        </div>
      </section>
    </main>
  </div>
</template>

<style scoped>
/* GENERAL STYLES */
.app-container { 
  display: flex; 
  min-height: 100vh; 
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; 
  transition: 0.3s; 
}
.dark { background: #121212; color: #e0e0e0; }
.light { background: #f0f2f5; color: #1a1a1a; }

/* SIDEBAR */
.sidebar { 
  width: 260px; 
  background: #1c2833; 
  color: white; 
  padding: 25px; 
  display: flex; 
  flex-direction: column; 
  box-shadow: 2px 0 5px rgba(0,0,0,0.1); 
}

.sidebar h2 { 
  color: #2ecc71; 
  margin-bottom: 40px; 
  font-size: 1.5rem; 
  letter-spacing: 1px; 
}

.sidebar ul { 
  list-style: none; 
  padding: 0; 
  margin-bottom: 20px; 
}


.sidebar li { 
  padding: 12px; 
  cursor: pointer;
  border-radius: 8px;
  transition: 0.2s;
}

.sidebar li:hover {
  background: #34495e;
}
</style>