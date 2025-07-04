# IRL-DS44
import { useState } from "react";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Checkbox } from "@/components/ui/checkbox";
import { Textarea } from "@/components/ui/textarea";
import { Card, CardHeader, CardTitle, CardContent } from "@/components/ui/card";
import { Download } from "lucide-react";

// 🗂️ Define the overall structure of the form data
const defaultData = {
  company: {
    name: "",
    rut: "",
    representative: "",
    address: "",
  },
  worker: {
    name: "",
    run: "",
    role: "",
    area: "",
    date: "",
    startTime: "",
    endTime: "",
    workplace: "",
    emergencyContact: "",
    emergencyPhone: "",
    status: {
      new: false,
      transfer: false,
      temp: false,
    },
  },
  workplace: {
    space: "",
    environment: "",
    order: "",
    machinery: "",
  },
  risks: [
    { hazard: "", measure: "" },
  ],
  procedures: [
    { doc: "", code: "", approved: false, notes: "" },
  ],
  substances: [
    { product: "", aspect: "", usage: "", limit: "", storage: "", epp: "", firstAid: "", hds: "" },
  ],
  medical: {
    hypertension: false,
    diabetes: false,
    asthma: false,
    allergies: false,
    other: "",
  },
  norms: {
    ley16744: false,
    manualHandling: false,
    epp: false,
    loto: false,
    signage: false,
    ast: false,
    emergency: false,
    fireExtinguisher: false,
    minsProtocols: false,
  },
};

const steps = [
  "Empresa",
  "Trabajador/a",
  "Lugar de Trabajo",
  "Riesgos",
  "Procedimientos",
  "Sustancias",
  "Ficha Médica",
  "Normas",
  "Revisión",
];

export default function App() {
  const [data, setData] = useState(defaultData);
  const [step, setStep] = useState(0);

  const next = () => setStep((s) => Math.min(s + 1, steps.length - 1));
  const prev = () => setStep((s) => Math.max(s - 1, 0));

  /* 📄 Generates a simple text representation of the IRL. Here you can
     integrate libraries like docx or pdf-lib to create downloadable files. */
  const generateDocument = () => {
    const blob = new Blob([JSON.stringify(data, null, 2)], { type: "application/json" });
    const url = URL.createObjectURL(blob);
    const link = document.createElement("a");
    link.href = url;
    link.download = `IRL_${data.company.name || "empresa"}.json`;
    link.click();
  };

  return (
    <div className="min-h-screen bg-gray-50 flex flex-col items-center p-4">
      {/* Wizard header */}
      <Card className="w-full max-w-5xl shadow-xl">
        <CardHeader>
          <CardTitle className="text-center text-2xl font-bold">
            Generador de Información de Riesgos Laborales (D.S. 44/2024)
          </CardTitle>
        </CardHeader>
        <CardContent>
          {/* Stepper */}
          <div className="w-full flex gap-2 mb-6 overflow-x-auto" aria-label="Paso actual">
            {steps.map((label, idx) => (
              <div
                key={label}
                className={`flex-1 text-center p-2 rounded-lg text-sm font-medium transition-all ${
                  idx === step
                    ? "bg-indigo-600 text-white"
                    : "bg-indigo-100 text-indigo-700"
                }`}
              >
                {label}
              </div>
            ))}
          </div>

          {/* Step content */}
          {step === 0 && (
            <CompanyStep data={data} setData={setData} />
          )}
          {step === 1 && (
            <WorkerStep data={data} setData={setData} />
          )}
          {step === 2 && (
            <WorkplaceStep data={data} setData={setData} />
          )}
          {step === 3 && (
            <RisksStep data={data} setData={setData} />
          )}
          {step === 4 && (
            <ProceduresStep data={data} setData={setData} />
          )}
          {step === 5 && (
            <SubstancesStep data={data} setData={setData} />
          )}
          {step === 6 && (
            <MedicalStep data={data} setData={setData} />
          )}
          {step === 7 && (
            <NormsStep data={data} setData={setData} />
          )}
          {step === 8 && (
            <ReviewStep data={data} />
          )}

          {/* Navigation buttons */}
          <div className="mt-8 flex justify-between">
            <Button variant="secondary" onClick={prev} disabled={step === 0}>
              Anterior
            </Button>
            {step < steps.length - 1 ? (
              <Button onClick={next}>Siguiente</Button>
            ) : (
              <Button onClick={generateDocument} className="gap-2">
                <Download size={16} /> Generar Documento
              </Button>
            )}
          </div>
        </CardContent>
      </Card>
    </div>
  );
}

/* ----------- Step Components ----------- */
function CompanyStep({ data, setData }) {
  const c = data.company;
  const update = (field) => (e) => setData({ ...data, company: { ...c, [field]: e.target.value } });
  return (
    <div className="grid gap-4 sm:grid-cols-2">
      <Input value={c.name} onChange={update("name")} placeholder="Razón Social" />
      <Input value={c.rut} onChange={update("rut")} placeholder="RUT Empresa" />
      <Input value={c.representative} onChange={update("representative")} placeholder="Representante Legal" />
      <Input value={c.address} onChange={update("address")} placeholder="Dirección" className="sm:col-span-2" />
    </div>
  );
}

function WorkerStep({ data, setData }) {
  const w = data.worker;
  const update = (field) => (e) => setData({ ...data, worker: { ...w, [field]: e.target.value } });
  const toggleStatus = (field) => () => setData({ ...data, worker: { ...w, status: { ...w.status, [field]: !w.status[field] } } });
  return (
    <div className="grid gap-4">
      <Input value={w.name} onChange={update("name")} placeholder="Nombre y Apellidos" />
      <div className="grid gap-4 sm:grid-cols-2">
        <Input value={w.run} onChange={update("run")} placeholder="RUN" />
        <Input value={w.role} onChange={update("role")} placeholder="Cargo" />
      </div>
      <div className="grid gap-4 sm:grid-cols-2">
        <Input value={w.area} onChange={update("area")} placeholder="Área" />
        <Input value={w.date} type="date" onChange={update("date")} />
      </div>
      <div className="grid gap-4 sm:grid-cols-2">
        <Input value={w.startTime} type="time" onChange={update("startTime")} label="Hora Inicio" />
        <Input value={w.endTime} type="time" onChange={update("endTime")} label="Hora Término" />
      </div>
      <Input value={w.workplace} onChange={update("workplace")} placeholder="Lugar de trabajo" />
      <div className="grid gap-4 sm:grid-cols-2">
        <Input value={w.emergencyContact} onChange={update("emergencyContact")} placeholder="Contacto de emergencia" />
        <Input value={w.emergencyPhone} onChange={update("emergencyPhone")} placeholder="Teléfono emergencia" />
      </div>
      <div className="flex items-center gap-6 flex-wrap mt-4">
        <label className="flex items-center gap-2 cursor-pointer">
          <Checkbox checked={w.status.new} onCheckedChange={toggleStatus("new")} /> Nuevo
        </label>
        <label className="flex items-center gap-2 cursor-pointer">
          <Checkbox checked={w.status.transfer} onCheckedChange={toggleStatus("transfer")} /> Transferido
        </label>
        <label className="flex items-center gap-2 cursor-pointer">
          <Checkbox checked={w.status.temp} onCheckedChange={toggleStatus("temp")} /> Temporal
        </label>
      </div>
    </div>
  );
}

function WorkplaceStep({ data, setData }) {
  const w = data.workplace;
  const update = (field) => (e) => setData({ ...data, workplace: { ...w, [field]: e.target.value } });
  return (
    <div className="grid gap-4">
      <Textarea value={w.space} onChange={update("space")} placeholder="Espacio de trabajo" />
      <Textarea value={w.environment} onChange={update("environment")} placeholder="Condiciones ambientales" />
      <Textarea value={w.order} onChange={update("order")} placeholder="Orden y aseo" />
      <Textarea value={w.machinery} onChange={update("machinery")} placeholder="Máquinas y herramientas" />
    </div>
  );
}

function RisksStep({ data, setData }) {
  const risks = data.risks;
  const addRisk = () => setData({ ...data, risks: [...risks, { hazard: "", measure: "" }] });
  const updateRisk = (idx, field) => (e) => {
    const newRisks = risks.slice();
    newRisks[idx][field] = e.target.value;
    setData({ ...data, risks: newRisks });
  };
  return (
    <div className="grid gap-6">
      {risks.map((r, idx) => (
        <div key={idx} className="grid gap-2 sm:grid-cols-2">
          <Input value={r.hazard} onChange={updateRisk(idx, "hazard")} placeholder={`Riesgo #${idx + 1}`} />
          <Input value={r.measure} onChange={updateRisk(idx, "measure")} placeholder="Medida preventiva" />
        </div>
      ))}
      <Button variant="secondary" onClick={addRisk} className="w-fit">Agregar Riesgo</Button>
    </div>
  );
}

function ProceduresStep({ data, setData }) {
  const list = data.procedures;
  const add = () => setData({ ...data, procedures: [...list, { doc: "", code: "", approved: false, notes: "" }] });
  const update = (idx, field) => (e) => {
    const copy = list.slice();
    copy[idx][field] = e.target.value;
    setData({ ...data, procedures: copy });
  };
  const toggleApproved = (idx) => () => {
    const copy = list.slice();
    copy[idx].approved = !copy[idx].approved;
    setData({ ...data, procedures: copy });
  };
  return (
    <div className="grid gap-6">
      {list.map((p, i) => (
        <div key={i} className="grid gap-2 sm:grid-cols-4 items-center">
          <Input value={p.doc} onChange={update(i, "doc")} placeholder="Documento / Actividad" className="sm:col-span-2" />
          <Input value={p.code} onChange={update(i, "code")} placeholder="Código" />
          <label className="flex items-center gap-2 cursor-pointer">
            <Checkbox checked={p.approved} onCheckedChange={toggleApproved(i)} /> Aprobado
          </label>
          <Input value={p.notes} onChange={update(i, "notes")} placeholder="Observ." className="sm:col-span-4" />
        </div>
      ))}
      <Button variant="secondary" onClick={add} className="w-fit">Agregar Procedimiento</Button>
    </div>
  );
}

function SubstancesStep({ data, setData }) {
  const list = data.substances;
  const add = () => setData({ ...data, substances: [...list, { product: "", aspect: "", usage: "", limit: "", storage: "", epp: "", firstAid: "", hds: "" }] });
  const update = (idx, field) => (e) => {
    const copy = list.slice();
    copy[idx][field] = e.target.value;
    setData({ ...data, substances: copy });
  };
  return (
    <div className="grid gap-6">
      {list.map((s, i) => (
        <div key={i} className="grid gap-2 sm:grid-cols-2">
          <Input value={s.product} onChange={update(i, "product")} placeholder="Producto" />
          <Input value={s.aspect} onChange={update(i, "aspect")} placeholder="Aspecto / Olor" />
          <Input value={s.usage} onChange={update(i, "usage")} placeholder="Modo de Empleo" />
          <Input value={s.limit} onChange={update(i, "limit")} placeholder="Límite de Exposición" />
          <Input value={s.storage} onChange={update(i, "storage")} placeholder="Almacenamiento" />
          <Input value={s.epp} onChange={update(i, "epp")} placeholder="EPP Requerido" />
          <Input value={s.firstAid} onChange={update(i, "firstAid")} placeholder="Primeros Auxilios" />
          <Input value={s.hds} onChange={update(i, "hds")} placeholder="Ficha HDS" />
        </div>
      ))}
      <Button variant="secondary" onClick={add} className="w-fit">Agregar Sustancia</Button>
    </div>
  );
}

function MedicalStep({ data, setData }) {
  const med = data.medical;
  const toggle = (field) => () => setData({ ...data, medical: { ...med, [field]: !med[field] } });
  return (
    <div className="grid gap-4">
      {[
        ["Hipertensión", "hypertension"],
        ["Diabetes", "diabetes"],
        ["Asma", "asthma"],
        ["Alergias", "allergies"],
      ].map(([label, key]) => (
        <label key={key} className="flex items-center gap-2 cursor-pointer">
          <Checkbox checked={med[key]} onCheckedChange={toggle(key)} /> {label}
        </label>
      ))}
      <Textarea value={med.other} onChange={(e) => setData({ ...data, medical: { ...med, other: e.target.value } })} placeholder="Otras observaciones médicas" />
    </div>
  );
}

function NormsStep({ data, setData }) {
  const n = data.norms;
  const toggle = (field) => () => setData({ ...data, norms: { ...n, [field]: !n[field] } });
  const normList = [
    ["ley16744", "Ley 16.744 – Accidentes del Trabajo"] ,
    ["manualHandling", "Manejo Manual de Materiales"],
    ["epp", "Uso de EPP"],
    ["loto", "Bloqueo / Etiquetado LOTO"],
    ["signage", "Señalización de Seguridad"],
    ["ast", "Análisis Seguro de Trabajo (AST)"],
    ["emergency", "Procedimiento de Emergencia y Evacuación"],
    ["fireExtinguisher", "Uso de Extintores"],
    ["minsProtocols", "Protocolos MINSAL (PREXOR, TMERT…)"],
  ];
  return (
    <div className="grid gap-2">
      {normList.map(([key, label]) => (
        <label key={key} className="flex items-center gap-2 cursor-pointer">
          <Checkbox checked={n[key]} onCheckedChange={toggle(key)} /> {label}
        </label>
      ))}
    </div>
  );
}

function ReviewStep({ data }) {
  return (
    <div className="prose max-w-none">
      <h3>Resumen de Información</h3>
      <pre className="whitespace-pre-wrap bg-gray-100 p-4 rounded-lg overflow-x-auto text-sm">
        {JSON.stringify(data, null, 2)}
      </pre>
      <p className="text-sm text-gray-500">Revisa los datos antes de generar el documento.</p>
    </div>
  );
}
