<script lang="ts">
  import dayjs from "dayjs";

  const currency = (value: number) =>
    new Intl.NumberFormat("id-ID", {
      style: "currency",
      currency: "IDR",
      maximumFractionDigits: 0,
    }).format(value);

  const availableLimit = 10_500_000;
  const DAILY_INTEREST = 0.003; // 0.3% per day

  type PlanId = "30d" | "3m" | "6m" | "12m";

  type Plan = { id: PlanId; label: string; months: number; days: number };
  type PaymentRow = { label: string; amount: number; date: string };

  const plans: Plan[] = [
    { id: "30d", label: "30 days", months: 1, days: 30 },
    { id: "3m", label: "3 months", months: 3, days: 90 },
    { id: "6m", label: "6 months", months: 6, days: 180 },
    { id: "12m", label: "12 months", months: 12, days: 360 },
  ];

  let amount = 2_000_000;
  let selectedPlanId: PlanId = "30d";
  let payments: PaymentRow[] = [];
  let showConfirm = false;

  $: selectedPlan = plans.find((p) => p.id === selectedPlanId) ?? plans[0];
  $: selectedInterestRate =
    selectedPlan.id === "30d" ? 0 : DAILY_INTEREST * selectedPlan.days;
  $: interestAmount = amount * selectedInterestRate;
  $: totalToPay = amount + interestAmount;
  $: monthlyInstallment =
    selectedPlan.months > 1 ? Math.ceil(totalToPay / selectedPlan.months) : totalToPay;

  $: payments =
    selectedPlan.id === "30d"
      ? [
          {
            label: "Payment",
            amount: totalToPay,
            date: dayjs().add(30, "day").format("DD MMM YYYY"),
          },
        ]
      : Array.from({ length: selectedPlan.months }, (_, idx) => {
          const due = dayjs().add(idx + 1, "month");
          return {
            label: `Payment ${idx + 1}`,
            amount: monthlyInstallment,
            date: due.format("DD MMM YYYY"),
          };
        });
</script>

<main class="min-h-screen bg-sky-50 text-slate-900">
  <div class="mx-auto flex min-h-screen max-w-md flex-col gap-4 px-4 py-6">
    <header class="flex items-center gap-3">
      <a href="/" class="flex h-8 w-8 items-center justify-center rounded-full bg-white shadow-sm">
        ‹
      </a>
      <div>
        <p class="text-[11px] font-medium uppercase tracking-[0.16em] text-slate-400">
          New self loan
        </p>
        <h1 class="text-base font-semibold">
          Take a loan now
        </h1>
      </div>
    </header>

    <section class="rounded-2xl bg-white px-4 py-3 shadow-sm">
      <p class="text-[11px] font-medium uppercase tracking-[0.14em] text-slate-400">
        Amount
      </p>
      <p class="mt-2 text-2xl font-semibold">
        {currency(amount)}
      </p>
      <p class="mt-1 text-[11px] text-slate-500">
        Available limit {currency(availableLimit)}
      </p>

      <div class="mt-4">
        <input
          type="range"
          min="0"
          max={availableLimit}
          step="50000"
          bind:value={amount}
          class="w-full accent-sky-500"
        />
        <div class="mt-1 flex justify-between text-[10px] text-slate-500">
          <span>{currency(0)}</span>
          <span>{currency(availableLimit)}</span>
        </div>
      </div>
    </section>

    <section class="rounded-2xl bg-white px-4 py-3 shadow-sm">
      <p class="text-[11px] font-medium uppercase tracking-[0.14em] text-slate-400">
        Plan
      </p>

      <div class="mt-3 grid grid-cols-2 gap-2 text-[11px]">
        {#each plans as plan}
          <button
            type="button"
            class={`rounded-xl border px-3 py-2 text-left ${
              plan.id === selectedPlanId
                ? "border-sky-500 bg-sky-50 text-sky-700"
                : "border-slate-200 bg-white text-slate-700"
            }`}
            on:click={() => (selectedPlanId = plan.id)}
          >
            <p class="text-xs font-semibold">
              {plan.label}
            </p>
            {#if plan.id === "30d"}
              <p class="mt-0.5 text-[10px] text-emerald-500">
                No interest, pay in 30 days
              </p>
            {:else}
              <p class="mt-0.5 text-[10px] text-slate-500">
                Fixed fee applied over the period
              </p>
            {/if}
          </button>
        {/each}
      </div>
    </section>

    <section class="rounded-2xl bg-white px-4 py-3 text-[11px] shadow-sm">
      <p class="text-[11px] font-medium uppercase tracking-[0.14em] text-slate-400">
        Payments
      </p>

      <div class="mt-2 space-y-1">
        {#each payments as row}
          <div class="flex items-baseline justify-between">
            <div>
              <p class="font-medium text-slate-800">
                {row.label}
              </p>
              <p class="text-[10px] text-slate-500">
                Due {row.date}
              </p>
            </div>
            <p class="text-xs font-semibold text-slate-900">
              {currency(row.amount)}
            </p>
          </div>
        {/each}
      </div>
    </section>

    <section class="rounded-2xl bg-white px-4 py-3 text-[11px] shadow-sm">
      <p class="text-[11px] font-medium uppercase tracking-[0.14em] text-slate-400">
        Summary
      </p>

      <div class="mt-2 space-y-1">
        <div class="flex justify-between">
          <span class="text-slate-500">Loan amount</span>
          <span class="font-medium text-slate-800">{currency(amount)}</span>
        </div>
        {#if selectedPlan.months > 1}
          <div class="flex justify-between">
            <span class="text-slate-500">Each payment</span>
            <span class="font-semibold text-slate-900">{currency(monthlyInstallment)}</span>
          </div>
        {/if}
      </div>
    </section>

    <button
      type="button"
      class="mt-auto w-full rounded-full bg-sky-500 px-4 py-2.5 text-sm font-semibold text-white shadow-md hover:bg-sky-400"
      on:click={() => (showConfirm = true)}
    >
      Proceed
    </button>
  </div>

  {#if showConfirm}
    <div class="fixed inset-0 z-20 flex items-center justify-center bg-slate-900/60 px-4">
      <div class="w-full max-w-sm rounded-2xl bg-white p-4 text-[11px] text-slate-900 shadow-xl">
        <p class="text-[11px] font-medium uppercase tracking-[0.14em] text-slate-400">
          Confirm loan
        </p>
        <p class="mt-2 text-sm font-semibold">
          Create this self-loan?
        </p>
        <p class="mt-1 text-[11px] text-slate-500">
          Amount {currency(amount)} with {payments.length} payment{payments.length === 1 ? "" : "s"}
          as shown above.
        </p>

        <div class="mt-4 flex gap-2 text-[11px]">
          <button
            type="button"
            class="flex-1 rounded-full border border-slate-200 px-3 py-2 font-medium text-slate-700 hover:bg-slate-50"
            on:click={() => (showConfirm = false)}
          >
            Cancel
          </button>
          <a
            href="/loan/success"
            class="flex-1 rounded-full bg-sky-500 px-3 py-2 text-center font-semibold text-white shadow-sm hover:bg-sky-400"
          >
            Yes, proceed
          </a>
        </div>
      </div>
    </div>
  {/if}
</main>

