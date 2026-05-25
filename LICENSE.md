import React from "react";

export default function FishingJournalMVP() { const translations = { en: { title: "Fishing AI Journal", best{t.conditions}: "{t.bestConditions}", aiSuggestion: "{t.aiSuggestion}", nextStep: "{t.nextStep}", fishingSessions: "{t.fishingSessions}", gps: "GPS", loadingWeather: "{t.loadingWeather}", temperatureLabel: "Temperature", pressureLabel: "Pressure", conditions: "Conditions", highProbability: "t.highProbability", moderate{t.conditions}: "t.moderateConditions", aiLearning: "t.aiLearning", dateColumn: "Date", locationColumn: "Location", fishColumn: "Fish", weatherColumn: "Weather", pressureColumn: "Pressure", temperatureColumn: "Temperature", resultColumn: "Result", phase1: "{t.phase1}", phase2: "{t.phase2}", howGrow: "{t.howGrow}" subtitle: "MVP for tracking fishing sessions and analyzing fish activity.", addSession: "Add Fishing Session", save: "{t.save}", weather: "Weather", pressure: "Pressure", temperature: "Temperature", fish: "Fish", location: "Location", date: "Date", aiAnalysis: "AI Analysis", liveWeather: "Live Weather", forecast: "Fish Forecast" }, ru: { title: "AI Журнал Рыболова", best{t.conditions}: "Лучшие условия", aiSuggestion: "AI Рекомендация", nextStep: "Следующий шаг", fishingSessions: "Сессии рыбалки", gps: "GPS", loadingWeather: "Загрузка погоды...", temperatureLabel: "Температура", pressureLabel: "Давление", conditions: "Условия", highProbability: "Сегодня высокая вероятность активности щуки.", moderate{t.conditions}: "Обнаружены умеренные условия для рыбалки.", aiLearning: "AI изучает вашу историю рыбалки. Добавьте больше данных для точных прогнозов.", dateColumn: "Дата", locationColumn: "Локация", fishColumn: "Рыба", weatherColumn: "Погода", pressureColumn: "Давление", temperatureColumn: "Температура", resultColumn: "Результат", phase1: "Этап 1", phase2: "Этап 2", howGrow: "Как проект может развиваться" subtitle: "MVP для анализа рыбалки и активности рыбы.", addSession: "Добавить рыбалку", save: "Сохранить", weather: "Погода", pressure: "Давление", temperature: "Температура", fish: "Рыба", location: "Локация", date: "Дата", aiAnalysis: "AI Анализ", liveWeather: "Погода сейчас", forecast: "Прогноз клёва" }, lt: { title: "AI Žvejo Žurnalas", best{t.conditions}: "Geriausios sąlygos", aiSuggestion: "AI rekomendacija", nextStep: "Kitas žingsnis", fishingSessions: "Žvejybos sesijos", gps: "GPS", loadingWeather: "Kraunami orai...", temperatureLabel: "Temperatūra", pressureLabel: "Slėgis", conditions: "Sąlygos", highProbability: "Šiandien didelė lydekos aktyvumo tikimybė.", moderate{t.conditions}: "Nustatytos vidutinės žvejybos sąlygos.", aiLearning: "AI mokosi iš jūsų žvejybos istorijos. Pridėkite daugiau duomenų tikslesnėms prognozėms.", dateColumn: "Data", locationColumn: "Vieta", fishColumn: "Žuvis", weatherColumn: "Orai", pressureColumn: "Slėgis", temperatureColumn: "Temperatūra", resultColumn: "Rezultatas", phase1: "1 etapas", phase2: "2 etapas", howGrow: "Kaip projektas gali augti" subtitle: "MVP žvejybos sesijoms ir žuvų aktyvumui analizuoti.", addSession: "Pridėti žvejybą", save: "Išsaugoti", weather: "Orai", pressure: "Slėgis", temperature: "Temperatūra", fish: "Žuvis", location: "Vieta", date: "Data", aiAnalysis: "AI Analizė", liveWeather: "Dabartiniai orai", forecast: "Žuvų prognozė" } };

const [language, setLanguage] = React.useState("en"); const t = translations[language]; const [entries, setEntries] = React.useState(() => { const saved = localStorage.getItem("fishing_entries"); return saved ? JSON.parse(saved) : [

{
  date: "2026-05-20",
  location: "Lake Green",
  fish: "Pike",
  weather: "Cloudy",
  pressure: "748 mm",
  temp: "16°C",
  result: "Excellent"
},
{
  date: "2026-05-22",
  location: "River North",
  fish: "Perch",
  weather: "Sunny",
  pressure: "756 mm",
  temp: "20°C",
  result: "Medium"
},
{
  date: "2026-05-24",
  location: "Lake Green",
  fish: "Pike",
  weather: "Rainy",
  pressure: "744 mm",
  temp: "13°C",
  result: "High activity"
}
  ];

});

const [form, setForm] = React.useState({ date: "", location: "", fish: "", weather: "", pressure: "", temp: "", result: "" });

const [gps, setGps] = React.useState(null); const [weatherData, setWeatherData] = React.useState(null); const [telegramUser, setTelegramUser] = React.useState(null);

React.useEffect(() => { localStorage.setItem("fishing_entries", JSON.stringify(entries)); }, [entries]);

React.useEffect(() => { if (navigator.geolocation) { navigator.geolocation.getCurrentPosition((position) => { setGps({ lat: position.coords.latitude.toFixed(4), lng: position.coords.longitude.toFixed(4) });

fetch(
      `https://api.openweathermap.org/data/2.5/weather?lat=${position.coords.latitude}&lon=${position.coords.longitude}&appid=YOUR_API_KEY&units=metric`
    )
      .then((res) => res.json())
      .then((data) => {
        setWeatherData({
          temp: data.main.temp,
          pressure: data.main.pressure,
          weather: data.weather[0].main
        });
      });
  });
}

if (window.Telegram && window.Telegram.WebApp) {
  const tg = window.Telegram.WebApp;
  tg.ready();

  setTelegramUser(tg.initDataUnsafe?.user || null);
}

}, []);

const addEntry = () => { if (!form.date || !form.location || !form.fish) return;

const pressureValue = parseInt(form.pressure, 10);

const newEntry = {
  ...form,
  pressure: form.pressure || "-",
  temp: form.temp || "-",
  weather: form.weather || weatherData?.weather || "Unknown",
  result:
    pressureValue >= 744 && pressureValue <= 750
      ? "High activity"
      : "Medium activity"
};

setEntries([
  newEntry,
  ...entries
]);

setForm({
  date: "",
  location: "",
  fish: "",
  weather: weatherData?.weather || "",
  pressure: weatherData?.pressure?.toString() || "",
  temp: weatherData?.temp?.toString() || "",
  result: ""
});

};

React.useEffect(() => { if (weatherData) { setForm((prev) => ({ ...prev, weather: weatherData.weather || prev.weather, pressure: weatherData.pressure?.toString() || prev.pressure, temp: weatherData.temp?.toString() || prev.temp })); } }, [weatherData]);

setForm({ date: "", location: "", fish: "", weather: "", pressure: "", temp: "", result: "" }); };

const aiInsight = () => { const pikeDays = entries.filter((e) => e.fish === "Pike"); const perchDays = entries.filter((e) => e.fish === "Perch");

if (pikeDays.length >= 2) {
  return "AI detected increased Pike activity during cloudy weather and pressure between 744–748 mm.";
}

if (perchDays.length >= 2) {
  return "Perch activity increases during warmer temperatures and evening hours.";
}

return "AI is learning from your fishing history. Add more sessions for deeper predictions.";

};

const fishForecast = () => { if (!weatherData) return "Waiting for weather data...";

if (
  weatherData.pressure >= 744 &&
  weatherData.pressure <= 750 &&
  weatherData.weather.includes("Cloud")
) {
  return "High probability of Pike activity today.";
}

return "Moderate fishing conditions detected.";

};

return ( <div className="min-h-screen bg-gray-100 p-6"> <div className="max-w-6xl mx-auto space-y-6"> {telegramUser && ( <div className="bg-blue-50 border border-blue-200 rounded-2xl p-4"> <p className="text-sm text-blue-900"> Connected Telegram user: {telegramUser.first_name} </p> </div> )} <div className="bg-white rounded-3xl shadow-lg p-6"> <div className="flex flex-col md:flex-row md:items-center md:justify-between gap-4 mb-4"> <div> <h1 className="text-4xl font-bold mb-2">{t.title}</h1> <p className="text-gray-600 text-lg">{t.subtitle}</p> </div>

<select
          value={language}
          onChange={(e) => setLanguage(e.target.value)}
          className="border rounded-xl px-4 py-2"
        >
          <option value="en">English</option>
          <option value="ru">Русский</option>
          <option value="lt">Lietuvių</option>
        </select>
      </div>
      
    </div>

    <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
      <div className="bg-white rounded-2xl shadow p-5">
        <h2 className="text-xl font-semibold mb-2">{t.bestConditions}</h2>
        <p className="text-gray-700">
          Pike activity is strongest during cloudy weather and pressure between 744–748 mm.
        </p>
      </div>

      <div className="bg-white rounded-2xl shadow p-5">
        <h2 className="text-xl font-semibold mb-2">{t.aiSuggestion}</h2>
        <p className="text-gray-700">
          AI predicts stronger fish activity during falling pressure and cloudy evening conditions.
        </p>
      </div>

      <div className="bg-white rounded-2xl shadow p-5">
        <h2 className="text-xl font-semibold mb-2">{t.nextStep}</h2>
        <p className="text-gray-700">
          Add GPS locations, moon phases, and lure tracking for deeper analytics.
        </p>
      </div>
    </div>

    <div className="bg-white rounded-3xl shadow-lg p-6">
      <h2 className="text-2xl font-bold mb-6">{t.addSession}</h2>

      <div className="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
        <input
          type="date"
          value={form.date}
          onChange={(e) => setForm({ ...form, date: e.target.value })}
          className="border rounded-xl p-3"
        />

        <input
          placeholder={t.location}
          value={form.location}
          onChange={(e) => setForm({ ...form, location: e.target.value })}
          className="border rounded-xl p-3"
        />

        <input
          placeholder={t.fish}
          value={form.fish}
          onChange={(e) => setForm({ ...form, fish: e.target.value })}
          className="border rounded-xl p-3"
        />

        <input
          placeholder={t.weather}
          value={form.weather}
          onChange={(e) => setForm({ ...form, weather: e.target.value })}
          className="border rounded-xl p-3"
        />

        <input
          placeholder={t.pressure}
          value={form.pressure}
          onChange={(e) => setForm({ ...form, pressure: e.target.value })}
          className="border rounded-xl p-3"
        />

        <input
          placeholder={t.temperature}
          value={form.temp}
          onChange={(e) => setForm({ ...form, temp: e.target.value })}
          className="border rounded-xl p-3"
        />
      </div>

      <div className="flex gap-4 mb-6 flex-wrap">
        <button
          type="button"
          onClick={() => addEntry()}
          className="bg-black text-white px-5 py-3 rounded-xl hover:opacity-90 transition"
        >
          {t.save}
        </button>

        <div className="bg-gray-100 px-4 py-3 rounded-xl text-sm">
          GPS: {gps ? `${gps.lat}, ${gps.lng}` : "Detecting location..."}
        </div>
      </div>

      <div className="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
        <div className="bg-gray-100 rounded-2xl p-4">
          <h3 className="font-semibold mb-2">{t.liveWeather}</h3>

          {weatherData ? (
            <div className="text-gray-700 space-y-1">
              <p>{t.temperatureLabel}: {weatherData.temp}°C</p>
              <p>{t.pressureLabel}: {weatherData.pressure} hPa</p>
              <p>{t.conditions}: {weatherData.weather}</p>
            </div>
          ) : (
            <p className="text-gray-500">{t.loadingWeather}</p>
          )}
        </div>

        <div className="bg-gray-100 rounded-2xl p-4">
          <h3 className="font-semibold mb-2">{t.forecast}</h3>
          <p className="text-gray-700">{fishForecast()}</p>
        </div>
      </div>

      <div className="bg-gray-100 rounded-2xl p-4 mb-6">
        <h3 className="font-semibold mb-2">{t.aiAnalysis}</h3>
        <p className="text-gray-700">{aiInsight()}</p>
      </div>

      <div className="rounded-2xl overflow-hidden mb-6 border">
        <iframe
          title="map"
          width="100%"
          height="300"
          loading="lazy"
          allowFullScreen
          src="https://maps.google.com/maps?q=54.6872,25.2797&z=10&output=embed"
        ></iframe>
      </div>

      <div className="flex items-center justify-between mb-4">
        <h2 className="text-2xl font-bold">{t.fishingSessions}</h2>
        <button className="bg-black text-white px-4 py-2 rounded-xl hover:opacity-90 transition">
          Add Session
        </button>
      </div>

      <div className="overflow-x-auto">
        <table className="w-full border-collapse">
          <thead>
            <tr className="text-left border-b">
              <th className="py-3">{t.dateColumn}</th>
              <th>{t.locationColumn}</th>
              <th>{t.fishColumn}</th>
              <th>{t.weatherColumn}</th>
              <th>{t.pressureColumn}</th>
              <th>{t.temperatureColumn}</th>
              <th>{t.resultColumn}</th>
            </tr>
          </thead>
          <tbody>
            {entries.map((entry, index) => (
              <tr key={index} className="border-b hover:bg-gray-50 transition">
                <td className="py-3">{entry.date}</td>
                <td>{entry.location}</td>
                <td>{entry.fish}</td>
                <td>{entry.weather}</td>
                <td>{entry.pressure}</td>
                <td>{entry.temp}</td>
                <td>
                  <span className="bg-gray-200 px-3 py-1 rounded-full text-sm">
                    {entry.result}
                  </span>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </div>

    <div className="bg-white rounded-3xl shadow-lg p-6">
      <h2 className="text-2xl font-bold mb-4">How This Can Grow</h2>

      <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div className="border rounded-2xl p-4">
          <h3 className="font-semibold text-lg mb-2">Phase 1</h3>
          <ul className="list-disc pl-5 text-gray-700 space-y-1">
            <li>Fishing log</li>
            <li>Weather integration</li>
            <li>Simple analytics</li>
            <li>AI recommendations</li>
          </ul>
        </div>

        <div className="border rounded-2xl p-4">
          <h3 className="font-semibold text-lg mb-2">Phase 2</h3>
          <ul className="list-disc pl-5 text-gray-700 space-y-1">
            <li>GPS heatmaps</li>
            <li>Real weather API</li>
            <li>Telegram Mini App integration</li>
            <li>AI fish prediction engine</li>
            <li>User accounts and cloud sync</li>
            <li>AI learning from user history</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</div>

); }
