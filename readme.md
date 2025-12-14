'use client'

import { useState, useEffect } from "react";
import { motion } from "framer-motion";

export default function UKCarValuation() {
  const [step, setStep] = useState(1);
  const [vehicle, setVehicle] = useState<any>(null);
  const [branches, setBranches] = useState<string[]>([]);
  const [valuation, setValuation] = useState<number | null>(null);

  const [form, setForm] = useState({
    reg: "",
    mileage: "",
    postcode: "",
    branch: "",
    date: "",
    time: "",
    email: "",
    phone: "",
    consent: false,
  });

  const lookupVehicle = () => {
    setVehicle({ make: "Ford", model: "Fiesta", year: 2019 });
    setStep(2);
  };

  const calculateValuation = () => {
    const base = 9000;
    const mileagePenalty = Number(form.mileage) * 0.03;
    const agePenalty = (new Date().getFullYear() - 2019) * 400;
    return Math.max(500, Math.round(base - mileagePenalty - agePenalty));
  };

  useEffect(() => {
    if (form.postcode) setBranches(["London", "Birmingham", "Manchester"]);
  }, [form.postcode]);

  const submitLead = () => {
    console.log("Lead submitted:", { vehicle, form, valuation });
    setStep(6);
  };

  return (
    <div style={{ minHeight: "100vh", padding: 20, background: "#f8fafc" }}>
      <motion.h1 initial={{ opacity: 0 }} animate={{ opacity: 1 }} style={{ textAlign: "center" }}>
        UK Car Valuation
      </motion.h1>

      {step === 1 && (
        <>
          <input placeholder="Vehicle Registration" onChange={e => setForm({ ...form, reg: e.target.value })} />
          <button onClick={lookupVehicle}>Get Valuation</button>
        </>
      )}

      {step === 2 && vehicle && (
        <>
          <p>{vehicle.year} {vehicle.make} {vehicle.model}</p>
          <input placeholder="Mileage" onChange={e => setForm({ ...form, mileage: e.target.value })} />
          <input placeholder="Postcode" onChange={e => setForm({ ...form, postcode: e.target.value })} />
          <button onClick={() => { setValuation(calculateValuation()); setStep(3); }}>
            Continue
          </button>
        </>
      )}

      {step === 3 && (
        <>
          <h2>£{valuation}</h2>
          <button onClick={() => setStep(4)}>Book Appointment</button>
        </>
      )}

      {step === 4 && (
        <>
          <select onChange={e => setForm({ ...form, branch: e.target.value })}>
            <option>Select branch</option>
            {branches.map(b => <option key={b}>{b}</option>)}
          </select>
          <input type="date" onChange={e => setForm({ ...form, date: e.target.value })} />
          <input type="time" onChange={e => setForm({ ...form, time: e.target.value })} />
          <button onClick={() => setStep(5)}>Continue</button>
        </>
      )}

      {step === 5 && (
        <>
          <input placeholder="Email" onChange={e => setForm({ ...form, email: e.target.value })} />
          <input placeholder="Phone" onChange={e => setForm({ ...form, phone: e.target.value })} />
          <label>
            <input type="checkbox" onChange={e => setForm({ ...form, consent: e.target.checked })} />
            I agree to be contacted
          </label>
          <button onClick={submitLead}>Confirm Appointment</button>
        </>
      )}

      {step === 6 && <h2>Booked successfully ✅</h2>}
    </div>
  );
}
