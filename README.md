import fs from "node:fs";

const path = "/home/ubuntu/marketing-do-futuro/client/src/pages/Home.tsx";
let source = fs.readFileSync(path, "utf8");

const marker = 'function ReportsView({ reportReady, setReportReady, handleSync, syncing }: { reportReady: boolean; setReportReady: (value: boolean) => void; handleSync: () => void; syncing: boolean }) {';
if (!source.includes(marker)) throw new Error("ReportsView marker not found");

const panel = `function ExportPanel() {
  const [contentUrl, setContentUrl] = useState("");
  const [contentLinks, setContentLinks] = useState<string[]>([]);

  const copyText = async (value: string, message: string) => {
    try {
      await navigator.clipboard.writeText(value);
      toast.success(message);
    } catch {
      toast("Não foi possível copiar automaticamente", { description: "Selecione e copie o conteúdo manualmente." });
    }
  };

  const shareUrl = window.location.origin + "/?share=eetika-metricas";
  const addContentLink = () => {
    const value = contentUrl.trim();
    if (!/^https?:\\/\\//i.test(value)) {
      toast("Cole um link válido", { description: "Use o link público do post, Reel ou vídeo." });
      return;
    }
    setContentLinks((current) => [...current, value]);
    setContentUrl("");
    toast.success("Conteúdo adicionado", { description: "O link já pode ser compartilhado." });
  };

  const downloadMetricsImage = () => {
    const svg = '<svg xmlns="http://www.w3.org/2000/svg" width="1200" height="760" viewBox="0 0 1200 760"><rect width="1200" height="760" rx="32" fill="#20273b"/><circle cx="1050" cy="120" r="160" fill="#b7f34a" opacity=".9"/><text x="72" y="92" fill="#b7f34a" font-family="Arial" font-size="22" font-weight="700">EETIKA · MARKETING DO FUTURO</text><text x="72" y="180" fill="white" font-family="Arial" font-size="54" font-weight="700">Resumo de métricas</text><text x="72" y="220" fill="#b8bfd0" font-family="Arial" font-size="24">Últimos 7 dias · Instagram</text><g font-family="Arial"><text x="72" y="360" fill="#b8bfd0" font-size="20">ALCANCE</text><text x="72" y="420" fill="white" font-size="52" font-weight="700">42,8 mil</text><text x="420" y="360" fill="#b8bfd0" font-size="20">LEADS</text><text x="420" y="420" fill="white" font-size="52" font-weight="700">184</text><text x="768" y="360" fill="#b8bfd0" font-size="20">CPL</text><text x="768" y="420" fill="white" font-size="52" font-weight="700">R$ 6,74</text><text x="72" y="610" fill="#d4f68f" font-size="24" font-weight="700">+31,4% de alcance na semana</text><text x="72" y="662" fill="#b8bfd0" font-size="20">Dados prontos para compartilhar com seu time.</text></g></svg>';
    const blob = new Blob([svg], { type: "image/svg+xml" });
    const url = URL.createObjectURL(blob);
    const image = new Image();
    image.onload = () => {
      const canvas = document.createElement("canvas");
      canvas.width = 1200; canvas.height = 760;
      canvas.getContext("2d")?.drawImage(image, 0, 0);
      URL.revokeObjectURL(url);
      const link = document.createElement("a");
      link.download = "eetika-metricas.png";
      link.href = canvas.toDataURL("image/png");
      link.click();
      toast.success("Imagem pronta", { description: "O card de métricas foi baixado em PNG." });
    };
    image.src = url;
  };

  return <div className="mb-6 rounded-2xl border border-[#dfe5bf] bg-[#fbfff1] p-5 shadow-[0_10px_30px_rgba(24,32,55,0.04)] sm:p-6"><div className="flex flex-col justify-between gap-4 sm:flex-row sm:items-start"><div><p className="mb-1 text-[10px] font-bold uppercase tracking-[0.18em] text-[#6b8e22]">Exportar sem planilha</p><h2 className="text-[18px] font-extrabold tracking-[-0.025em] text-[#20273b]">Compartilhe métricas e conteúdos</h2><p className="mt-1.5 max-w-[620px] text-[12px] leading-5 text-[#74805d]">Baixe um card de métricas em imagem, copie um link compartilhável ou organize links de posts, Reels e vídeos.</p></div><div className="flex shrink-0 gap-2"><button onClick={downloadMetricsImage} className="inline-flex items-center gap-2 rounded-xl bg-[#20273b] px-3.5 py-2.5 text-[11px] font-bold text-white hover:bg-[#30384e]"><Download className="h-3.5 w-3.5" />Baixar PNG</button><button onClick={() => copyText(shareUrl, "Link de métricas copiado")} className="inline-flex items-center gap-2 rounded-xl border border-[#d8e4b4] bg-white px-3.5 py-2.5 text-[11px] font-bold text-[#5e7e1e] hover:border-[#b7d873]"><Link2 className="h-3.5 w-3.5" />Copiar link</button></div></div><div className="mt-5 flex flex-col gap-2 sm:flex-row"><input value={contentUrl} onChange={(event) => setContentUrl(event.target.value)} onKeyDown={(event) => event.key === "Enter" && addContentLink()} placeholder="Cole o link de um post, Reel ou vídeo" className="min-w-0 flex-1 rounded-xl border border-[#e0e7cc] bg-white px-3.5 py-2.5 text-xs text-[#30384e] outline-none placeholder:text-[#a7ae9a] focus:border-[#9abf48]" /><button onClick={addContentLink} className="inline-flex items-center justify-center gap-2 rounded-xl bg-[#b7f34a] px-4 py-2.5 text-[11px] font-extrabold text-[#486319] hover:bg-[#c7fa6b]"><Plus className="h-3.5 w-3.5" />Adicionar link</button></div>{contentLinks.length > 0 && <div className="mt-4 space-y-2">{contentLinks.map((link, index) => <div key={link + index} className="flex items-center gap-2 rounded-xl bg-white px-3 py-2.5 text-xs"><span className="min-w-0 flex-1 truncate text-[#596176]">{link}</span><button onClick={() => copyText(link, "Link copiado")} className="rounded-lg p-1.5 text-[#5b52d6] hover:bg-[#f1f0ff]"><Copy className="h-3.5 w-3.5" /></button><a href={link} target="_blank" rel="noreferrer" className="rounded-lg p-1.5 text-[#7c8496] hover:bg-[#f1f2f6]"><ExternalLink className="h-3.5 w-3.5" /></a></div>)}</div>}</div>;
}

`;
source = source.replace(marker, panel + marker);
const fnStart = source.indexOf(marker);
const returnStart = source.indexOf('  return <><div className="mb-7', fnStart);
if (returnStart < 0) throw new Error("ReportsView return not found");
source = source.slice(0, returnStart) + '  return <><ExportPanel /><div className="mb-7' + source.slice(returnStart + '  return <><div className="mb-7'.length);
fs.writeFileSync(path, source);
