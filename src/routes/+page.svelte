<script lang="ts">
  const currency = (value: number) =>
    new Intl.NumberFormat("id-ID", {
      style: "currency",
      currency: "IDR",
      maximumFractionDigits: 0,
    }).format(value);

  const savingBalance = 15_000_000;
  const totalOutstanding = 4_500_000;
  const availableLimit = savingBalance - totalOutstanding;

  const ongoingLoan = {
    name: "Gadget Upgrade",
    amount: 3_000_000,
    remaining: 2_000_000,
    nextDueDate: "10 Feb 2026",
    nextInstallmentAmount: 1_000_000,
  };
</script>

<main class="min-h-screen bg-sky-50 text-slate-900">
  <div class="mx-auto flex min-h-screen max-w-md flex-col gap-4 px-4 py-6">
    <section class="w-full rounded-3xl bg-gradient-to-br from-sky-500 via-sky-400 to-sky-600 p-4 shadow-xl">
      <div class="flex items-center justify-between">
        <div>
          <p class="text-[11px] font-medium uppercase tracking-[0.16em] text-sky-100">
            Self loan
          </p>
          <h1 class="mt-1 text-lg font-semibold text-white">
            Your self-funded limit
          </h1>
        </div>
        <div class="flex h-9 w-9 items-center justify-center rounded-2xl bg-sky-700/80 text-xs font-bold text-white">
          SL
        </div>
      </div>

      <div class="mt-4 rounded-2xl bg-white/95 px-4 py-3 text-slate-900 shadow-sm">
        <p class="text-xs text-slate-500">
          Remaining limit
        </p>
        <p class="mt-1 text-2xl font-semibold">
          {currency(availableLimit)}
        </p>
        <p class="mt-1 text-[11px] text-slate-500">
          From total limit {currency(savingBalance)}
        </p>

        <div class="mt-3 h-1.5 overflow-hidden rounded-full bg-slate-200">
          <div
            class="h-full rounded-full bg-sky-500"
            style={`width: ${(availableLimit / savingBalance) * 100}%`}
          />
        </div>
        <p class="mt-1 text-[11px] text-slate-500">
          {(availableLimit / savingBalance * 100).toFixed(0)}% of your limit is still available.
        </p>
      </div>

      <a
        href="/loan"
        class="mt-4 block w-full rounded-full bg-white px-4 py-2.5 text-center text-sm font-semibold text-sky-600 shadow-md hover:bg-sky-50"
      >
        Take a loan now
      </a>
    </section>

    <section class="w-full rounded-2xl bg-white px-4 py-3 text-slate-900 shadow-sm">
      <div class="mb-2 flex items-center justify-between">
        <div>
          <p class="text-[11px] font-medium uppercase tracking-[0.14em] text-slate-400">
            Ongoing loan
          </p>
          <p class="text-sm font-semibold">
            {ongoingLoan.name}
          </p>
        </div>
        <p class="text-xs font-medium text-slate-500">
          Outstanding {currency(ongoingLoan.remaining)}
        </p>
      </div>

      <div class="mt-1 flex items-center justify-between text-[11px]">
        <div>
          <p class="text-slate-500">
            Original amount
          </p>
          <p class="font-semibold">
            {currency(ongoingLoan.amount)}
          </p>
        </div>
        <div class="text-right">
          <p class="text-slate-500">
            Next payment
          </p>
          <p class="font-semibold">
            {currency(ongoingLoan.nextInstallmentAmount)}
          </p>
          <p class="text-[10px] text-slate-500">
            Due {ongoingLoan.nextDueDate}
          </p>
        </div>
      </div>
    </section>
  </div>
</main>

