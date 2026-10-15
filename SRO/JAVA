// ========== ДАННЫЕ ==========
let tariffs = [
  { id: 1, name: "Мой онлайн", operator: "Т2", price: 500, gb: 30, minutes: 400, sms: 100 },
  { id: 2, name: "bee HIT", operator: "Билайн", price: 700, gb: 50, minutes: 700, sms: 700 },
  { id: 3, name: "Максимум", operator: "МегаФон", price: 700, gb: 40, minutes: 800, sms: 0 },
  { id: 4, name: "МТС Супер", operator: "МТС", price: 800, gb: 50, minutes: 1000, sms: 500 },
  { id: 5, name: "Оптимальный", operator: "Т-Мобайл", price: 600, gb: 25, minutes: 600, sms: 100 },
  { id: 6, name: "Минимум", operator: "МегаФон", price: 400, gb: 10, minutes: 200, sms: 0 },
  { id: 7, name: "Мой разговор", operator: "Т2", price: 400, gb: 10, minutes: 200, sms: 100 }
];

let nextId = 8;
let selectedIds = new Set();
let filtered = [...tariffs];

// ========== ВСПОМОГАТЕЛЬНЫЕ ФУНКЦИИ ==========
function $(id) {
  return document.getElementById(id);
}

function validatePositive(value, fieldName) {
  if (value === "" || isNaN(value) || Number(value) < 0) {
    alert(`Поле «${fieldName}» должно быть неотрицательным числом`);
    return false;
  }
  return true;
}

// ========== ОТРИСОВКА ==========
function renderTable(data = filtered) {
  const tbody = $("tariffTable");
  $("count").textContent = `(${data.length})`;

  if (data.length === 0) {
    tbody.innerHTML = `<tr><td colspan="8" class="empty">Тарифы не найдены</td></tr>`;
    return;
  }

  tbody.innerHTML = data.map(t => `
    <tr>
      <td class="checkbox-cell">
        <input type="checkbox" ${selectedIds.has(t.id) ? "checked" : ""} 
               onchange="toggleSelect(${t.id})">
      </td>
      <td><strong>${t.name}</strong></td>
      <td>${t.operator}</td>
      <td>${t.price} ₽</td>
      <td>${t.gb} ГБ</td>
      <td>${t.minutes}</td>
      <td>${t.sms}</td>
      <td>
        <button class="btn btn-danger btn-sm" onclick="deleteTariff(${t.id})">Удалить</button>
      </td>
    </tr>
  `).join("");

  updateCalcSelect();
}

function updateCalcSelect() {
  const select = $("calcTariff");
  select.innerHTML = tariffs.map(t =>
    `<option value="${t.id}">${t.name} (${t.operator}) — ${t.price} ₽</option>`
  ).join("");
}

// ========== ДЕЙСТВИЯ ==========
function addTariff() {
  const name = $("name").value.trim();
  const operator = $("operator").value.trim();
  const price = $("price").value;
  const gb = $("gb").value;
  const minutes = $("minutes").value;
  const sms = $("sms").value;

  if (!name || !operator) {
    alert("Название и оператор обязательны");
    return;
  }
  if (!validatePositive(price, "Цена") ||
      !validatePositive(gb, "ГБ") ||
      !validatePositive(minutes, "Минуты") ||
      !validatePositive(sms, "SMS")) {
    return;
  }

  tariffs.push({
    id: nextId++,
    name,
    operator,
    price: Number(price),
    gb: Number(gb),
    minutes: Number(minutes),
    sms: Number(sms)
  });

  // Очистка формы
  ["name", "operator", "price", "gb", "minutes", "sms"].forEach(id => {
    $(id).value = "";
  });

  applyFilters();
}

function deleteTariff(id) {
  if (!confirm("Удалить тариф?")) return;
  tariffs = tariffs.filter(t => t.id !== id);
  selectedIds.delete(id);
  applyFilters();
}

function toggleSelect(id) {
  if (selectedIds.has(id)) {
    selectedIds.delete(id);
  } else {
    selectedIds.add(id);
  }
}

function clearSelection() {
  selectedIds.clear();
  renderTable();
  $("compareResult").innerHTML = "";
}

function applyFilters() {
  const maxPrice = $("filterPrice").value;
  const minGb = $("filterGb").value;
  const minMin = $("filterMinutes").value;

  filtered = tariffs.filter(t => {
    if (maxPrice !== "" && t.price > Number(maxPrice)) return false;
    if (minGb !== "" && t.gb < Number(minGb)) return false;
    if (minMin !== "" && t.minutes < Number(minMin)) return false;
    return true;
  });

  renderTable();
  $("compareResult").innerHTML = "";
}

function resetFilters() {
  $("filterPrice").value = "";
  $("filterGb").value = "";
  $("filterMinutes").value = "";
  filtered = [...tariffs];
  renderTable();
}

function compareSelected() {
  const selected = tariffs.filter(t => selectedIds.has(t.id));
  const box = $("compareResult");

  if (selected.length < 2) {
    box.innerHTML = `
      <div class="result-box error">
        <h3>Выберите минимум 2 тарифа для сравнения</h3>
      </div>`;
    return;
  }

  // Сортировка по цене
  selected.sort((a, b) => a.price - b.price);

  let html = `
    <div class="result-box">
      <h3>Сравнение выбранных тарифов</h3>
      <div class="table-wrapper">
        <table>
          <thead>
            <tr>
              <th>Параметр</th>
              ${selected.map(t => `<th>${t.name}<br><small>${t.operator}</small></th>`).join("")}
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>Цена</td>
              ${selected.map(t => `<td><strong>${t.price} ₽</strong></td>`).join("")}
            </tr>
            <tr>
              <td>Интернет</td>
              ${selected.map(t => `<td>${t.gb} ГБ</td>`).join("")}
            </tr>
            <tr>
              <td>Минуты</td>
              ${selected.map(t => `<td>${t.minutes}</td>`).join("")}
            </tr>
            <tr>
              <td>SMS</td>
              ${selected.map(t => `<td>${t.sms}</td>`).join("")}
            </tr>
            <tr>
              <td>Цена за 1 ГБ*</td>
              ${selected.map(t => `<td>${t.gb > 0 ? (t.price / t.gb).toFixed(1) : "—"} ₽</td>`).join("")}
            </tr>
          </tbody>
        </table>
      </div>
      <p style="margin-top:10px;font-size:0.85rem;color:var(--muted)">
        * Примерная стоимость 1 ГБ внутри пакета
      </p>
    </div>`;

  box.innerHTML = html;
}

function calculateCost() {
  const id = Number($("calcTariff").value);
  const usedGb = Number($("usedGb").value);
  const overage = Number($("overage").value);
  const box = $("calcResult");

  if ($("usedGb").value === "" || isNaN(usedGb) || usedGb < 0) {
    alert("Укажите корректный расход интернета");
    return;
  }
  if (isNaN(overage) || overage < 0) {
    alert("Укажите корректную цену перерасхода");
    return;
  }

  const t = tariffs.find(x => x.id === id);
  if (!t) return;

  const extraGb = Math.max(0, usedGb - t.gb);
  const extraCost = extraGb * overage;
  const total = t.price + extraCost;

  box.innerHTML = `
    <div class="result-box">
      <h3>Результат расчёта</h3>
      <p><strong>Тариф:</strong> ${t.name} (${t.operator})</p>
      <p><strong>Базовая плата:</strong> ${t.price} ₽</p>
      <p><strong>Ваш расход:</strong> ${usedGb} ГБ (в тарифе ${t.gb} ГБ)</p>
      <p><strong>Перерасход:</strong> ${extraGb.toFixed(1)} ГБ × ${overage} ₽ = ${extraCost.toFixed(0)} ₽</p>
      <p style="font-size:1.2rem;margin-top:8px">
        <strong>Итого: ${total.toFixed(0)} ₽ / мес</strong>
      </p>
    </div>`;
}

// ========== ЗАПУСК ==========
renderTable();
