<template>
  <div class="font-sans text-slate-300 pb-8">

    <!-- Header -->
    <div class="flex items-start justify-between mb-8">
      <div>
        <p class="text-[11px] uppercase tracking-[1.5px] text-slate-500 mb-1">{{ todayLabel }}</p>
        <h1 class="font-display text-2xl font-extrabold text-white tracking-tight">Dashboard</h1>
      </div>
      <div class="flex items-center gap-2 bg-emerald-500/10 border border-emerald-500/20 text-emerald-400
                  text-[11px] font-semibold uppercase tracking-wider px-3 py-1.5 rounded-full">
        <span class="live-dot"></span>
        Live
      </div>
    </div>

    <!-- Stats Grid -->
    <div class="grid grid-cols-1 sm:grid-cols-3 gap-3.5 mb-6">
      <div
        v-for="(stat, i) in statCards"
        :key="i"
        class="stat-card relative overflow-hidden rounded-2xl border border-white/[0.07] p-5
               bg-white/[0.03] hover:-translate-y-0.5 hover:border-white/[0.12]
               transition-all duration-200 cursor-default"
        :style="`--accent: ${stat.color}; animation-delay: ${i * 0.1}s`"
      >
        <!-- Top glow line -->
        <div class="stat-topline absolute inset-x-0 top-0 h-px opacity-40"></div>

        <div class="flex items-center justify-between mb-4">
          <div class="w-9 h-9 rounded-[9px] flex items-center justify-center stat-icon-bg border stat-icon-border"
               v-html="stat.icon"></div>
          <span class="text-[10px] font-semibold uppercase tracking-wider px-2 py-1 rounded-full"
                :class="stat.trendUp
                  ? 'text-emerald-400 bg-emerald-500/10 border border-emerald-500/15'
                  : 'text-blue-400 bg-blue-500/10 border border-blue-500/15'">
            {{ stat.trend }}
          </span>
        </div>

        <p class="text-[11px] uppercase tracking-[0.8px] text-slate-500 mb-1.5">{{ stat.label }}</p>
        <h3 class="font-display text-3xl font-bold text-white tracking-tighter leading-none">{{ stat.value }}</h3>

        <div class="mt-4 h-0.5 rounded-full bg-white/[0.06]">
          <div class="h-full rounded-full opacity-70 stat-bar-fill transition-all duration-700"
               :style="`width: ${stat.fill}%`"></div>
        </div>
      </div>
    </div>

    <!-- Transaksi Table -->
    <div class="rounded-2xl border border-white/[0.07] bg-white/[0.02] overflow-hidden">

      <div class="flex items-center justify-between px-6 py-4 border-b border-white/[0.05]">
        <div class="flex items-center gap-2.5">
          <div class="w-[3px] h-4 bg-indigo-400 rounded-full"></div>
          <h3 class="font-display text-sm font-bold text-slate-100">Transaksi Terbaru</h3>
        </div>
        <span class="flex items-center gap-1.5 text-[11px] font-semibold text-amber-400
                     bg-amber-500/[0.08] border border-amber-500/15 px-2.5 py-1 rounded-full">
          <svg width="11" height="11" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5">
            <polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/>
          </svg>
          {{ transaksi.length }} data
        </span>
      </div>

      <div class="overflow-x-auto">
        <table class="w-full border-collapse">
          <thead>
            <tr class="border-b border-white/[0.05]">
              <th v-for="h in ['#','Waktu','Kasir','Total','Bayar','Kembalian']" :key="h"
                  class="px-5 py-3 text-left text-[10px] font-semibold uppercase tracking-[1px] text-slate-600">
                {{ h }}
              </th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(t, idx) in transaksi"
              :key="t.id"
              class="table-row border-b border-white/[0.03] last:border-0
                     hover:bg-white/[0.025] transition-colors duration-150"
              :style="`animation-delay: ${idx * 0.04}s`"
            >
              <td class="px-5 py-3.5">
                <span class="font-mono text-[11px] text-slate-600">{{ idx + 1 }}</span>
              </td>
              <td class="px-5 py-3.5">
                <div class="flex flex-col gap-0.5">
                  <span class="text-xs font-medium text-slate-300">{{ formatDate(t.created_at).date }}</span>
                  <span class="font-mono text-[11px] text-slate-500">{{ formatDate(t.created_at).time }}</span>
                </div>
              </td>
              <td class="px-5 py-3.5">
                <div class="flex items-center gap-2.5 text-slate-300 text-[13px]">
                  <div class="w-6 h-6 rounded-[7px] flex items-center justify-center
                              text-[10px] font-bold text-amber-900 shrink-0
                              bg-gradient-to-br from-amber-400 to-amber-600">
                    {{ t.kasir?.name?.charAt(0) }}
                  </div>
                  {{ t.kasir?.name }}
                </div>
              </td>
              <td class="px-5 py-3.5">
                <span class="font-mono text-xs font-medium text-emerald-400
                             bg-emerald-500/[0.08] border border-emerald-500/15 px-2.5 py-1 rounded-md">
                  {{ formatRupiah(t.total) }}
                </span>
              </td>
              <td class="px-5 py-3.5">
                <span class="font-mono text-xs text-slate-500">{{ formatRupiah(t.bayar) }}</span>
              </td>
              <td class="px-5 py-3.5">
                <span class="font-mono text-xs font-medium text-blue-400
                             bg-blue-500/[0.08] border border-blue-500/15 px-2.5 py-1 rounded-md">
                  {{ formatRupiah(t.kembalian) }}
                </span>
              </td>
            </tr>

            <tr v-if="transaksi.length === 0">
              <td colspan="6">
                <div class="flex flex-col items-center gap-3 py-16 text-slate-600 text-[13px]">
                  <div class="w-14 h-14 rounded-2xl bg-white/[0.03] border border-white/[0.06]
                              flex items-center justify-content-center justify-center">
                    <svg width="28" height="28" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5">
                      <path d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" stroke-linecap="round"/>
                    </svg>
                  </div>
                  <p>Belum ada transaksi hari ini</p>
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import api from '@/api/axios'

const transaksi = ref([])

onMounted(async () => {
  const res = await api.get('/transaksi')
  transaksi.value = res.data.data
})

const today = new Date().toDateString()
const todayLabel = new Date().toLocaleDateString('id-ID', {
  weekday: 'long', day: 'numeric', month: 'long', year: 'numeric'
})

const stats = computed(() => {
  const hariIni = transaksi.value.filter(
    t => new Date(t.created_at).toDateString() === today
  )
  return {
    transaksiHariIni: hariIni.length,
    pendapatanHariIni: hariIni.reduce((s, t) => s + Number(t.total), 0),
    totalTransaksi: transaksi.value.length,
  }
})

const statCards = computed(() => [
  {
    label: 'Transaksi Hari Ini',
    value: stats.value.transaksiHariIni,
    color: '#f59e0b',
    trend: '+hari ini',
    trendUp: true,
    fill: Math.min(stats.value.transaksiHariIni * 10, 100),
    icon: `<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="#f59e0b" stroke-width="1.8"><path d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2" stroke-linecap="round"/></svg>`
  },
  {
    label: 'Pendapatan Hari Ini',
    value: formatRupiah(stats.value.pendapatanHariIni),
    color: '#10b981',
    trend: 'revenue',
    trendUp: true,
    fill: 65,
    icon: `<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="#10b981" stroke-width="1.8"><path d="M12 2v20M17 5H9.5a3.5 3.5 0 000 7h5a3.5 3.5 0 010 7H6" stroke-linecap="round"/></svg>`
  },
  {
    label: 'Total Semua Transaksi',
    value: stats.value.totalTransaksi,
    color: '#60a5fa',
    trend: 'akumulasi',
    trendUp: false,
    fill: 80,
    icon: `<svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="#60a5fa" stroke-width="1.8"><polyline points="22 12 18 12 15 21 9 3 6 12 2 12" stroke-linecap="round" stroke-linejoin="round"/></svg>`
  }
])

function formatRupiah(n) {
  return new Intl.NumberFormat('id-ID', {
    style: 'currency', currency: 'IDR', maximumFractionDigits: 0
  }).format(n)
}

function formatDate(d) {
  const dt = new Date(d)
  return {
    date: dt.toLocaleDateString('id-ID', { day: '2-digit', month: 'short' }),
    time: dt.toLocaleTimeString('id-ID', { hour: '2-digit', minute: '2-digit' })
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&family=Syne:wght@700;800&family=DM+Mono:wght@400;500&display=swap');

* { box-sizing: border-box; }

.font-sans    { font-family: 'Plus Jakarta Sans', sans-serif; }
.font-display { font-family: 'Syne', sans-serif; }
.font-mono    { font-family: 'DM Mono', monospace; }

/* Live dot */
.live-dot {
  width: 6px; height: 6px;
  border-radius: 50%;
  background: #10b981;
  animation: pulse 2s infinite;
}
@keyframes pulse {
  0%,100% { opacity: 1; transform: scale(1); }
  50%     { opacity: .4; transform: scale(.8); }
}

/* Stat card accent uses CSS var --accent */
.stat-topline   { background: linear-gradient(90deg, transparent, var(--accent), transparent); }
.stat-icon-bg   { background: color-mix(in srgb, var(--accent) 12%, transparent); }
.stat-icon-border { border-color: color-mix(in srgb, var(--accent) 25%, transparent); }
.stat-bar-fill  { background: var(--accent); }

/* Stat card entrance */
.stat-card { animation: slideUp .5s cubic-bezier(.16,1,.3,1) both; }
@keyframes slideUp {
  from { opacity: 0; transform: translateY(18px); }
  to   { opacity: 1; transform: translateY(0); }
}

/* Table row entrance */
.table-row { animation: rowIn .4s cubic-bezier(.16,1,.3,1) both; }
@keyframes rowIn {
  from { opacity: 0; transform: translateX(-6px); }
  to   { opacity: 1; transform: translateX(0); }
}
</style>