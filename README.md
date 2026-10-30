
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<meta name="theme-color" content="#101827">
<title>Meu Futuro</title>
<style>
*{box-sizing:border-box}
body{margin:0;padding:18px 16px 40px;background:#101827;color:#fff;font-family:Arial,sans-serif}
header{text-align:center;padding:22px 0}
header h1{color:#42e8a3;font-size:30px;margin:0}
header p{font-size:11px;letter-spacing:2px;color:#aab7c9}
nav{display:flex;gap:7px;overflow:auto;margin-bottom:16px}
button{border:0;border-radius:9px;padding:13px;background:#42e8a3;color:#102018;font-weight:bold;cursor:pointer}
nav button{white-space:nowrap;background:#263449;color:white}
nav button.active{background:#42e8a3;color:#102018}
.card{background:#1d293b;padding:17px;border-radius:15px;margin:13px 0}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:10px}
small,.muted{color:#aab7c9}
.value{font-size:21px;color:#42e8a3;font-weight:bold;margin-top:9px;overflow-wrap:anywhere}
label{display:block;margin:13px 0 6px;color:#b9c5d6;font-size:14px}
input,select{width:100%;padding:13px;border-radius:8px;border:1px solid #44516a;background:#101827;color:white;font-size:16px}
section{display:none}
section.active{display:block}
.item{padding:12px 0;border-bottom:1px solid #34445b;display:flex;justify-content:space-between;gap:8px}
.item:last-child{border:0}
.green{color:#42e8a3}
.red{color:#ff8585}
.bar{height:10px;background:#34445b;border-radius:10px;overflow:hidden;margin-top:12px}
.bar div{height:100%;background:#42e8a3}
.secondary{background:#34445b;color:white}
.danger{background:#713d48;color:white}
.full{width:100%;margin-top:15px}
.smallbtn{padding:7px 10px;font-size:12px}
</style>
</head>
<body>
<header>
<h1>MEU FUTURO</h1>
<p>DISCIPLINA HOJE, LIBERDADE AMANHÃ.</p>
</header>

<nav>
<button class="active" onclick="aba('inicio',this)">Início</button>
<button onclick="aba('financas',this)">Finanças</button>
<button onclick="aba('investimentos',this)">Investimentos</button>
<button onclick="aba('historico',this)">Histórico</button>
</nav>

<section id="inicio" class="active">
<div class="grid">
<div class="card"><small>Saldo financeiro</small><div class="value" id="saldo">R$ 0,00</div></div>
<div class="card"><small>Investimentos</small><div class="value" id="patrimonio">R$ 0,00</div></div>
<div class="card"><small>Receitas</small><div class="value" id="receitas">R$ 0,00</div></div>
<div class="card"><small>Despesas</small><div class="value" id="despesas">R$ 0,00</div></div>
</div>
<div class="card">
<h3>Minha meta mensal</h3>
<div class="value" id="metaValor">R$ 3.000,00</div>
<div class="bar"><div id="barra" style="width:0%"></div></div>
<p class="muted" id="progresso">0% da meta alcançada</p>
<label>Alterar meta (R$)</label>
<input id="metaInput" type="number" min="1" value="3000">
<button class="full" onclick="salvarMeta()">Salvar meta</button>
</div>
<div class="card">
<h3>Meu patrimônio total</h3>
<div class="value" id="totalPatrimonio">R$ 0,00</div>
<small>Saldo financeiro + investimentos</small>
</div>
</section>

<section id="financas">
<div class="card">
<h3>Registrar movimentação</h3>
<label>Tipo</label>
<select id="tipo">
<option value="receita">Receita / ganho</option>
<option value="despesa">Despesa / gasto</option>
</select>
<label>Descrição</label>
<input id="descricao" placeholder="Ex: Entregas, combustível, alimentação">
<label>Valor (R$)</label>
<input id="valor" type="number" min="0.01" step="0.01" placeholder="0,00">
<button class="full" onclick="registrar()">Registrar</button>
</div>
<div class="card">
<h3>Resumo financeiro</h3>
<p>Ganhos: <strong class="green" id="resReceitas">R$ 0,00</strong></p>
<p>Gastos: <strong class="red" id="resDespesas">R$ 0,00</strong></p>
<p>Saldo: <strong class="green" id="resSaldo">R$ 0,00</strong></p>
</div>
</section>

<section id="investimentos">
<div class="card">
<h3>Registrar investimento</h3>
<label>Modalidade</label>
<select id="modalidade">
<option>Renda fixa</option>
<option>Fundos imobiliários (FIIs)</option>
<option>Ações</option>
<option>Tesouro Direto</option>
<option>CDB</option>
<option>Criptomoedas</option>
<option>Poupança</option>
</select>
<label>Operação</label>
<select id="operacao">
<option value="aporte">Aporte</option>
<option value="resgate">Resgate</option>
</select>
<label>Valor (R$)</label>
<input id="valorInvest" type="number" min="0.01" step="0.01" placeholder="0,00">
<button class="full" onclick="registrarInvestimento()">Salvar investimento</button>
</div>
<div class="card">
<h3>Meu patrimônio por modalidade</h3>
<div id="listaInvestimentos"></div>
</div>
<div class="card">
<small>Os valores representam os aportes menos os resgates registrados. Rendimentos e cotações precisam ser atualizados separadamente. Esta versão não fornece recomendações de compra.</small>
</div>
</section>

<section id="historico">
<div class="card">
<h3>Histórico de movimentações</h3>
<div id="listaHistorico"></div>
<button class="danger full" onclick="limparHistorico()">Apagar todos os registros</button>
</div>
</section>

<script>
const chave="meuFuturoDadosV2";
let dados={movimentos:[],investimentos:[],meta:3000};

try{
const salvo=JSON.parse(localStorage.getItem(chave));
if(salvo && Array.isArray(salvo.movimentos) && Array.isArray(salvo.investimentos)){
dados={...dados,...salvo};
}
}catch(e){}

const el=id=>document.getElementById(id);
const moeda=n=>Number(n).toLocaleString("pt-BR",{style:"currency",currency:"BRL"});
const modalidades=["Renda fixa","Fundos imobiliários (FIIs)","Ações","Tesouro Direto","CDB","Criptomoedas","Poupança"];

function aba(nome,botao){
document.querySelectorAll("section").forEach(s=>s.classList.remove("active"));
document.querySelectorAll("nav button").forEach(b=>b.classList.remove("active"));
el(nome).classList.add("active");
botao.classList.add("active");
}

function totais(){
const receitas=dados.movimentos.filter(x=>x.tipo==="receita").reduce((s,x)=>s+x.valor,0);
const despesas=dados.movimentos.filter(x=>x.tipo==="despesa").reduce((s,x)=>s+x.valor,0);
const patrimonio=dados.investimentos.reduce((s,x)=>s+(x.operacao==="aporte"?x.valor:-x.valor),0);
return {receitas,despesas,saldo:receitas-despesas,patrimonio};
}

function salvar(){
try{
localStorage.setItem(chave,JSON.stringify(dados));
atualizar();
}catch(e){
alert("Não foi possível salvar. Verifique o espaço disponível no navegador.");
}
}

function registrar(){
const tipo=el("tipo").value;
const descricao=el("descricao").value.trim();
const valor=Number(el("valor").value);
if(!descricao||!Number.isFinite(valor)||valor<=0){
alert("Preencha a descrição e um valor válido.");return;
}
dados.movimentos.push({tipo,descricao,valor,data:new Date().toISOString()});
el("descricao").value="";
el("valor").value="";
salvar();
alert("Movimentação registrada!");
}

function registrarInvestimento(){
const modalidade=el("modalidade").value;
const operacao=el("operacao").value;
const valor=Number(el("valorInvest").value);
if(!Number.isFinite(valor)||valor<=0){
alert("Digite um valor válido.");return;
}
const atual=dados.investimentos.filter(x=>x.modalidade===modalidade)
.reduce((s,x)=>s+(x.operacao==="aporte"?x.valor:-x.valor),0);
if(operacao==="resgate"&&valor>atual){
alert("O resgate não pode superar o saldo registrado nessa modalidade.");return;
}
dados.investimentos.push({modalidade,operacao,valor,data:new Date().toISOString()});
el("valorInvest").value="";
salvar();
alert("Investimento registrado!");
}

function salvarMeta(){
const valor=Number(el("metaInput").value);
if(!Number.isFinite(valor)||valor<=0){
alert("Digite uma meta válida.");return;
}
dados.meta=valor;
salvar();
alert("Meta atualizada!");
}

function atualizar(){
const t=totais();
el("saldo").textContent=moeda(t.saldo);
el("patrimonio").textContent=moeda(t.patrimonio);
el("receitas").textContent=moeda(t.receitas);
el("despesas").textContent=moeda(t.despesas);
el("resReceitas").textContent=moeda(t.receitas);
el("resDespesas").textContent=moeda(t.despesas);
el("resSaldo").textContent=moeda(t.saldo);
el("metaValor").textContent=moeda(dados.meta);
el("metaInput").value=dados.meta;
const pct=dados.meta>0?Math.max(0,t.receitas/dados.meta*100):0;
el("barra").style.width=Math.min(100,pct)+"%";
el("progresso").textContent=pct.toFixed(1)+"% da meta alcançada";
el("totalPatrimonio").textContent=moeda(t.saldo+t.patrimonio);

el("listaInvestimentos").innerHTML=modalidades.map(m=>{
const valor=dados.investimentos.filter(x=>x.modalidade===m)
.reduce((s,x)=>s+(x.operacao==="aporte"?x.valor:-x.valor),0);
return '<div class="item"><span>'+m+'</span><strong class="green">'+moeda(valor)+'</strong></div>';
}).join("");

const todos=[
...dados.movimentos.map((x,i)=>({...x,categoria:"financeiro",indice:i})),
...dados.investimentos.map((x,i)=>({...x,categoria:"investimento",indice:i}))
].sort((a,b)=>new Date(b.data)-new Date(a.data));

el("listaHistorico").innerHTML=todos.length?todos.map(x=>{
const titulo=x.categoria==="financeiro"?x.descricao:x.modalidade;
const sinal=x.categoria==="financeiro"?(x.tipo==="receita"?"+":"-"):(x.operacao==="aporte"?"+":"-");
const data=new Date(x.data).toLocaleDateString("pt-BR");
return '<div class="item"><div><strong>'+titulo+'</strong><br><small>'+data+' • '+(x.categoria==="financeiro"?x.tipo:x.operacao)+'</small></div><strong class="'+(sinal==="+"?"green":"red")+'">'+sinal+moeda(x.valor)+'</strong></div>';
}).join(""):'<p class="muted">Nenhuma movimentação registrada ainda.</p>';
}

function limparHistorico(){
if(!confirm("Tem certeza que deseja apagar todos os registros? Essa ação não pode ser desfeita."))return;
dados.movimentos=[];
dados.investimentos=[];
salvar();
}

atualizar();
</script>
</body>
</html>
