<script setup>
import { computed, ref } from 'vue'

const reservas = ref([
  {
    id: 1,
    hospede: 'Carlos Silva',
    quarto: 'Suíte 201',
    entrada: '2026-09-22',
    saida: '2026-09-25',
    status: 'Confirmada',
    valor: 1250
  },
  {
    id: 2,
    hospede: 'Ana Souza',
    quarto: 'Quarto 105',
    entrada: '2026-09-23',
    saida: '2026-09-27',
    status: 'Pendente',
    valor: 980
  },
  {
    id: 3,
    hospede: 'João Oliveira',
    quarto: 'Suíte 302',
    entrada: '2026-09-24',
    saida: '2026-09-29',
    status: 'Confirmada',
    valor: 1750
  },
  {
    id: 4,
    hospede: 'Mariana Costa',
    quarto: 'Quarto 110',
    entrada: '2026-09-25',
    saida: '2026-09-28',
    status: 'Cancelada',
    valor: 720
  }
])

const pesquisa = ref('')
const filtroStatus = ref('Todos')
const ordenarPor = ref('hospede')
const mostrarModal = ref(false)
const reservaSelecionada = ref(null)

const reservasFiltradas = computed(() => {
  let resultado = reservas.value.filter(reserva => {
    const texto = pesquisa.value.toLowerCase()

    const correspondePesquisa =
      reserva.hospede.toLowerCase().includes(texto) ||
      reserva.quarto.toLowerCase().includes(texto)

    const correspondeStatus =
      filtroStatus.value === 'Todos' ||
      reserva.status === filtroStatus.value

    return correspondePesquisa && correspondeStatus
  })

  return resultado.sort((a, b) => {
    if (ordenarPor.value === 'hospede') {
      return a.hospede.localeCompare(b.hospede)
    }

    if (ordenarPor.value === 'valor') {
      return b.valor - a.valor
    }

    if (ordenarPor.value === 'entrada') {
      return new Date(a.entrada) - new Date(b.entrada)
    }

    return 0
  })
})

const totalReservas = computed(() => reservas.value.length)

const confirmadas = computed(() =>
  reservas.value.filter(r => r.status === 'Confirmada').length
)

const pendentes = computed(() =>
  reservas.value.filter(r => r.status === 'Pendente').length
)

const faturamento = computed(() =>
  reservas.value
    .filter(r => r.status !== 'Cancelada')
    .reduce((total, r) => total + r.valor, 0)
)

function abrirReserva(reserva) {
  reservaSelecionada.value = reserva
  mostrarModal.value = true
}

function cancelarReserva() {
  if (!reservaSelecionada.value) return

  const reserva = reservas.value.find(
    r => r.id === reservaSelecionada.value.id
  )

  if (reserva) {
    reserva.status = 'Cancelada'
  }

  mostrarModal.value = false
}

function formatarValor(valor) {
  return new Intl.NumberFormat('pt-BR', {
    style: 'currency',
    currency: 'BRL'
  }).format(valor)
}

function formatarData(data) {
  return new Date(data + 'T00:00:00').toLocaleDateString('pt-BR')
}
</script>

<template>
  <main class="dashboard">

    <header class="header">
      <div>
        <span class="tag">HOTEL.PET</span>
        <h1>Dashboard de Reservas</h1>
        <p>Gerenciamento inteligente do seu hotel.</p>
      </div>

      <button class="new-button">
        + Nova reserva
      </button>
    </header>

    <section class="cards">

      <div class="card">
        <span>Total de reservas</span>
        <strong>{{ totalReservas }}</strong>
        <small>Reservas cadastradas</small>
      </div>

      <div class="card green">
        <span>Confirmadas</s