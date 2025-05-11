<script setup>
import { ref, computed } from 'vue'

const daftarBuku = ref([])
const judulBaru = ref('')
const pengarangBaru = ref('')
const hasilPencarian = ref('')
const pesanError = ref('')
const pesanSukses = ref('')

function tambahBuku() {

  pesanError.value = ''
  pesanSukses.value = ''

  const judul = judulBaru.value.trim()
  const pengarang = pengarangBaru.value.trim()

  if (!judul || !pengarang) {
    pesanError.value = 'Judul dan pengarang di isi dulu napa.'
    return
  }

  const duplikat = daftarBuku.value.find(b => b.judul === judul && b.pengarang === pengarang)
  if (duplikat) {
    alert('kembali mengisi yang belum ada')
    pesanError.value = 'Buku sudah ada.'
    return
  }

  daftarBuku.value.push({ judul, pengarang })
  daftarBuku.value.sort((a, b) => a.judul.localeCompare(b.judul))
  pesanSukses.value = '✅ Buku berhasil ditambahkan.'

  judulBaru.value = ''
  pengarangBaru.value = ''
  setTimeout(() => pesanSukses.value = '', 2000)
}

function hapusBuku(judul, pengarang) {
  const konfirmasi = window.confirm(`Yakin mau menghapus buku "${judul}" oleh ${pengarang}?`)
  if (!konfirmasi) return

  daftarBuku.value = daftarBuku.value.filter(
    b => !(b.judul === judul && b.pengarang === pengarang)
  )
}

const bukuYangDicari = computed(() => {
  if (!hasilPencarian.value.trim()) return daftarBuku.value
  return daftarBuku.value.filter(b =>
    b.pengarang.toLowerCase().includes(hasilPencarian.value.toLowerCase())
  )
})

</script>

<template>
  <div class="min-h-screen bg-gradient-to-br bg-[#171717]  p-6 flex justify-center items-start">
    <div class="w-full max-w-6xl flex flex-col lg:flex-row gap-6">


      <div class="flex-1 backdrop-blur-lg border bg-[#e7e7e8] border-white/40 rounded-xl shadow-xl p-8 space-y-6">
        <h1 class="text-4xl font-extrabold text-white text-center tracking-tight drop-shadow-md"> Perpustakaan Mini</h1>

        <div class="grid sm:grid-cols-2 gap-4">
          <input
            v-model="judulBaru"
            placeholder=" Judul Buku"
            class="p-3 rounded-lg border border-white/60 bg-white/70 backdrop-blur-sm placeholder-gray-600 focus:ring-2 focus:ring-blue-400"
          />
          <input
            v-model="pengarangBaru"
            placeholder=" Nama Pengarang"
            class="p-3 rounded-lg border border-white/60 bg-white/70 backdrop-blur-sm placeholder-gray-600 focus:ring-2 focus:ring-blue-400"
          />
        </div>

        <button
          @click="tambahBuku"
          class="w-full bg-gradient-to-r from-indigo-500 to-blue-500 text-white font-semibold py-3 rounded-lg hover:opacity-90 transition"
        >
          + Tambah Buku
        </button>

        <div v-if="pesanError" class="text-red-100 bg-red-500/70 px-4 py-2 rounded text-lg">{{ pesanError }}</div>
        <div v-if="pesanSukses" class="text-green-100 bg-green-500/70 px-4 py-2 rounded text-lg">{{ pesanSukses }}</div>

        <input
          v-model="hasilPencarian"
          placeholder="🔎 Cari pengarang..."
          class="w-full p-3 rounded-lg border border-white/60 bg-white/70 placeholder-gray-600 backdrop-blur-sm focus:ring-2 focus:ring-purple-400"
        />
      </div>

      <div class="flex-1 backdrop-blur-lg bg-white/40 border border-white/40 rounded-xl shadow-xl p-8 space-y-4 overflow-y-auto max-h-[80vh]">
        <h2 class="text-2xl text-white font-bold mb-2 text-center drop-shadow"> Daftar Buku ({{ bukuYangDicari.length }})</h2>
        <div v-if="bukuYangDicari.length === 0" class="text-white/80 italic text-center">Belum ada buku.</div>
        <div class="grid gap-4">
          <transition-group name="fade" tag="div">
            <div
              v-for="(buku,) in bukuYangDicari"
              :key="buku.judul + buku.pengarang"
              class="bg-white/60 border border-white/40 backdrop-blur-md rounded-lg p-4 flex justify-between items-center shadow hover:shadow-lg transition"
            >
              <div>
                <p class="text-lg font-bold text-gray-900">{{ buku.judul }}</p>
                <p class="text-sm text-gray-700 italic">oleh {{ buku.pengarang }}</p>
              </div>
              <button
                @click="hapusBuku(buku.judul, buku.pengarang)"
                class="text-red-600 hover:text-red-800 text-sm font-medium"
              >
                ⛔ Hapus
              </button>
            </div>
          </transition-group>
        </div>
      </div>

    </div>
  </div>
</template>


<style scoped>
.fade-enter-active, .fade-leave-active {
  transition: all 0.4s ease;
}
.fade-enter-from, .fade-leave-to {
  opacity: 0;
  transform: translateY(12px);
}
</style>
