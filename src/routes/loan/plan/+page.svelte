<script lang="ts">
  const currency = (value: number) =>
    new Intl.NumberFormat("id-ID", {
      style: "currency",
      currency: "IDR",
      maximumFractionDigits: 0,
    }).format(value);

  const loanId = "ID-104483";
  const totalAmount = 3_000_000;
  const remaining = 2_000_000;

  type PaymentStatus = "paid" | "upcoming";

  type PaymentCard = {
    id: number;
    date: string;
    amount: number;
    status: PaymentStatus;
  };

  const paymentCards: PaymentCard[] = [
    { id: 1, date: "10 Jan 2026", amount: 1_000_000, status: "paid" },
    { id: 2, date: "10 Feb 2026", amount: 1_000_000, status: "upcoming" },
    { id: 3, date: "10 Mar 2026", amount: 1_000_000, status: "upcoming" },
  ];
</script>

<main class="min-h-screen bg-sky-50 text-slate-900">
  <div class="mx-auto flex min-h-screen max-w-md flex-col gap-4 px-4 py-6">
    <header class="flex items-center gap-3">
      <a href="/" class="flex h-8 w-8 items-center justify-center rounded-full bg-white shadow-sm">
        ‹
      </a>
      <div>
        <p class="text-[11px] font-medium uppercase tracking-[0.16em] text-slate-400">
          Ongoing self-loan
        </p>
        <h1 class="text-base font-semibold">
          {loanId}
        </h1>
      </div>
    </header>

    <section class="rounded-2xl bg-white px-4 py-3 text-[11px] shadow-sm">
      <p class="text-[10px] font-medium uppercase tracking-[0.14em] text-slate-400">
        You still owe yourself
      </p>
      <p class="mt-1 text-lg font-semibold text-slate-900">
        {currency(remaining)}
      </p>
      <p class="text-[11px] text-slate-500">
        of {currency(totalAmount)} total
      </p>

      <div class="mt-3 h-1.5 overflow-hidden rounded-full bg-slate-200">
        <div
          class="h-full rounded-full bg-sky-500"
          style={`width: ${(1 - remaining / totalAmount) * 100}%`}
        />
      </div>
      <p class="mt-1 text-[10px] text-slate-500">
        {((1 - remaining / totalAmount) * 100).toFixed(0)}% paid so far
      </p>
    </section>

    <section class="space-y-3 text-[11px]">
      {#each paymentCards as card}
        <div class="rounded-2xl bg-white px-4 py-3 shadow-sm">
          <div class="mb-2 flex items-center justify-between">
            <div>
              <p class="text-xs font-semibold text-slate-900">
                Payment {card.id}
              </p>
              <p class="text-[10px] text-slate-500">
                Due {card.date}
              </p>
            </div>

            {#if card.status === "paid"}
              <span class="rounded-full bg-emerald-50 px-2 py-1 text-[10px] font-medium text-emerald-600">
                Paid
              </span>
            {:else}
              <span class="rounded-full bg-sky-50 px-2 py-1 text-[10px] font-medium text-sky-600">
                Upcoming
              </span>
            {/if}
          </div>

          <div class="mt-1 flex items-center justify-between">
            <p class="text-[10px] text-slate-500">
              Amount
            </p>
            <p class="text-sm font-semibold text-slate-900">
              {currency(card.amount)}
            </p>
          </div>
        </div>
      {/each}
    </section>
  </div>
</main>

