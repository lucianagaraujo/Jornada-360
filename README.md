import React, { useState, useEffect } from 'react';
import { LineChart, Line, XAxis, YAxis, CartesianGrid, Tooltip, Legend, ResponsiveContainer, Area, AreaChart, ReferenceLine } from 'recharts';

const JornadaFinanceira360 = () => {
  // Estados para dados pessoais
  const [idade, setIdade] = useState(30);
  const [expectativaVida, setExpectativaVida] = useState(85);
  const [rendaMensal, setRendaMensal] = useState(5000);
  const [patrimonioFinanceiro, setPatrimonioFinanceiro] = useState(100000);
  const [patrimonioImoveis, setPatrimonioImoveis] = useState(0);
  const [aporteRecorrente, setAporteRecorrente] = useState(1000);
  const [idadeMeta, setIdadeMeta] = useState(65);
  
  // Estados para variáveis econômicas
  const [retornoInvestimento, setRetornoInvestimento] = useState(6);
  const [retornoImoveis, setRetornoImoveis] = useState(3);
  const [inflacao, setInflacao] = useState(4);
  const [nivelEstiloVida, setNivelEstiloVida] = useState(80);
  
  // Estados para fatores emocionais
  const [confortoDecisoes, setConfortoDecisoes] = useState(5);
  const [importanciaSeguranca, setImportanciaSeguranca] = useState(5);
  const [toleranciaRisco, setToleranciaRisco] = useState(5);
  
  // Estado para mostrar resultado emocional
  const [mostrarResultadoEmocional, setMostrarResultadoEmocional] = useState(false);
  
  // Estado para dados de projeção
  const [dadosProjecao, setDadosProjecao] = useState([]);
  const [scoreLiberdade, setScoreLiberdade] = useState(0);
  const [horizonteSeguranca, setHorizonteSeguranca] = useState(0);
  const [rendaIndependencia, setRendaIndependencia] = useState(0);
  
  // Estado para mostrar ou esconder detalhes avançados
  const [mostrarAvancado, setMostrarAvancado] = useState(false);
  
  // Estados para cenários
  const [cenarioAtual, setCenarioAtual] = useState('base');
  const [cenarios, setCenarios] = useState({
    base: {
      retornoInvestimento: 6,
      retornoImoveis: 3,
      inflacao: 4,
      nivelEstiloVida: 80
    },
    otimista: {
      retornoInvestimento: 8,
      retornoImoveis: 5,
      inflacao: 3,
      nivelEstiloVida: 80
    },
    pessimista: {
      retornoInvestimento: 4,
      retornoImoveis: 2,
      inflacao: 6,
      nivelEstiloVida: 80
    }
  });
  
  // Efeito para calcular projeção quando os valores mudam
  useEffect(() => {
    calcularProjecao();
  }, [idade, expectativaVida, rendaMensal, patrimonioFinanceiro, patrimonioImoveis, 
     aporteRecorrente, idadeMeta, retornoInvestimento, retornoImoveis, inflacao, nivelEstiloVida]);
  
  // Função para aplicar cenário
  const aplicarCenario = (cenario) => {
    setCenarioAtual(cenario);
    setRetornoInvestimento(cenarios[cenario].retornoInvestimento);
    setRetornoImoveis(cenarios[cenario].retornoImoveis);
    setInflacao(cenarios[cenario].inflacao);
    setNivelEstiloVida(cenarios[cenario].nivelEstiloVida);
  };
  
  // Função para salvar cenário personalizado
  const salvarCenario = () => {
    const novosCenarios = {...cenarios};
    novosCenarios.personalizado = {
      retornoInvestimento: retornoInvestimento,
      retornoImoveis: retornoImoveis,
      inflacao: inflacao,
      nivelEstiloVida: nivelEstiloVida
    };
    setCenarios(novosCenarios);
    setCenarioAtual('personalizado');
    alert('Cenário personalizado salvo!');
  };
  
  // Função para calcular projeção
  const calcularProjecao = () => {
    const anosAteMeta = idadeMeta - idade;
    const anosEmIndependencia = expectativaVida - idadeMeta;
    const retornoRealAnualFinanceiro = (1 + retornoInvestimento/100) / (1 + inflacao/100) - 1;
    const retornoRealAnualImoveis = (1 + retornoImoveis/100) / (1 + inflacao/100) - 1;
    
    let patrimonioFinanceiroAcumulado = patrimonioFinanceiro;
    let patrimonioImoveisAcumulado = patrimonioImoveis;
    let patrimonioTotalAcumulado = patrimonioFinanceiroAcumulado + patrimonioImoveisAcumulado;
    let projecao = [];
    
    // Fase de acumulação
    for (let ano = 0; ano <= anosAteMeta; ano++) {
      const idadeAtual = idade + ano;
      
      // Atualiza patrimônio financeiro com retorno financeiro + aportes
      patrimonioFinanceiroAcumulado = patrimonioFinanceiroAcumulado * (1 + retornoRealAnualFinanceiro) + (aporteRecorrente * 12);
      
      // Atualiza patrimônio de imóveis com retorno de imóveis
      patrimonioImoveisAcumulado = patrimonioImoveisAcumulado * (1 + retornoRealAnualImoveis);
      
      // Calcula patrimônio total
      patrimonioTotalAcumulado = patrimonioFinanceiroAcumulado + patrimonioImoveisAcumulado;
      
      projecao.push({
        idade: idadeAtual,
        fase: 'Acumulação',
        patrimonio: patrimonioTotalAcumulado.toFixed(2),
        patrimonioFinanceiro: patrimonioFinanceiroAcumulado.toFixed(2),
        patrimonioImoveis: patrimonioImoveisAcumulado.toFixed(2),
        renda: rendaMensal.toFixed(2)
      });
    }
    
    // Calcular renda na independência financeira
    const rendaIndependenciaMensal = (rendaMensal * (nivelEstiloVida/100));
    setRendaIndependencia(rendaIndependenciaMensal);
    
    // Fase de desacumulação
    let anosComPatrimonio = 0;
    for (let ano = 1; ano <= anosEmIndependencia; ano++) {
      const idadeAtual = idadeMeta + ano;
      
      // Imóveis continuam a se valorizar
      patrimonioImoveisAcumulado = patrimonioImoveisAcumulado * (1 + retornoRealAnualImoveis);
      
      // Renda é retirada apenas do patrimônio financeiro
      // Primeiro o retorno do patrimônio financeiro
      patrimonioFinanceiroAcumulado = patrimonioFinanceiroAcumulado * (1 + retornoRealAnualFinanceiro);
      
      // Depois desconta a renda mensal
      patrimonioFinanceiroAcumulado -= (rendaIndependenciaMensal * 12);
      
      // Se o patrimônio financeiro ficar negativo, começa a "consumir" os imóveis
      // (representando venda ou uso de renda de aluguel)
      if (patrimonioFinanceiroAcumulado < 0) {
        patrimonioImoveisAcumulado += patrimonioFinanceiroAcumulado;
        patrimonioFinanceiroAcumulado = 0;
      }
      
      // Calcula patrimônio total
      patrimonioTotalAcumulado = patrimonioFinanceiroAcumulado + patrimonioImoveisAcumulado;
      
      // Se ainda há patrimônio total positivo
      if (patrimonioTotalAcumulado > 0) {
        anosComPatrimonio = ano;
      }
      
      projecao.push({
        idade: idadeAtual,
        fase: 'Independência',
        patrimonio: Math.max(0, patrimonioTotalAcumulado).toFixed(2),
        patrimonioFinanceiro: Math.max(0, patrimonioFinanceiroAcumulado).toFixed(2),
        patrimonioImoveis: Math.max(0, patrimonioImoveisAcumulado).toFixed(2),
        renda: rendaIndependenciaMensal.toFixed(2)
      });
    }
    
    // Calcula o score de liberdade e horizonte de segurança
    const scoreLib = (anosComPatrimonio / anosEmIndependencia) * 100;
    setScoreLiberdade(scoreLib.toFixed(0));
    setHorizonteSeguranca(anosComPatrimonio);
    
    // Atualiza dados de projeção
    setDadosProjecao(projecao);
  };
  
  // Função para exportar como PDF
  const exportarPDF = () => {
    window.print();
  };
  
  // Calcular perfil emocional
  const calcularPerfilEmocional = () => {
    setMostrarResultadoEmocional(true);
    
    // Esta parte seria expandida com uma análise mais detalhada
    // baseada nos estados emocionais e nos resultados financeiros
  };
  
  // Mapear score de liberdade para cor
  const mapearCorScore = (score) => {
    if (score >= 100) return '#4CAF50'; // Verde
    if (score >= 80) return '#8BC34A'; // Verde claro
    if (score >= 60) return '#FFEB3B'; // Amarelo
    if (score >= 40) return '#FFC107'; // Âmbar
    if (score >= 20) return '#FF9800'; // Laranja
    return '#F44336'; // Vermelho
  };
  
  // Texto de análise emocional
  const obterTextoAnaliseEmocional = () => {
    if (scoreLiberdade >= 100) {
      return `Seu plano financeiro proporciona uma forte tranquilidade mental. Com um Score de Liberdade de ${scoreLiberdade}%, você tem recursos suficientes para toda a sua jornada de independência e possivelmente deixar um legado. Esta solidez ajuda a reduzir a tensão financeira e permite que você viva plenamente esta fase com serenidade.`;
    } else if (scoreLiberdade >= 80) {
      return `Seu plano financeiro está bem estruturado, mas pode se beneficiar de alguns ajustes para maior tranquilidade. Com um Score de Liberdade de ${scoreLiberdade}%, você tem cobertura para a maior parte da sua jornada de independência. Considerando seu perfil financeiro emocional, recomendamos considerar aumentar levemente seus aportes mensais para atingir 100% de cobertura.`;
    } else if (scoreLiberdade >= 60) {
      return `Seu plano financeiro está no caminho certo, mas requer atenção. Com um Score de Liberdade de ${scoreLiberdade}%, seus recursos podem ser insuficientes para toda a jornada de independência. Considerando seu mindset financeiro, este nível intermediário pode gerar alguma insegurança. Considere aumentar seus aportes recorrentes ou ajustar suas expectativas de estilo de vida.`;
    } else if (scoreLiberdade >= 40) {
      return `Seu plano financeiro precisa de ajustes significativos. Com um Score de Liberdade de ${scoreLiberdade}%, há um risco considerável de ficar sem recursos durante sua fase de independência. Para seu biótipo financeiro, esta situação pode gerar insegurança elevada. Recomendamos rever urgentemente sua estratégia de investimentos, possivelmente buscando orientação profissional.`;
    } else {
      return `Seu plano financeiro atual apresenta desafios substanciais. Com um Score de Liberdade de ${scoreLiberdade}%, é provável que seu patrimônio seja insuficiente para financiar sua jornada de independência por muito tempo. Esta situação pode intensificar sentimentos de insegurança financeira. Recomendamos uma revisão completa de sua estratégia, incluindo aumentar significativamente seus aportes mensais, reconsiderar a idade de transição ou ajustar suas expectativas de estilo de vida.`;
    }
  };
  
  return (
    <div className="bg-gray-50 font-sans p-4 max-w-6xl mx-auto">
      <header className="text-center mb-6">
        <h1 className="text-3xl font-bold text-indigo-700 mb-2">Jornada Financeira 360°</h1>
        <p className="text-gray-600">Mapeamento de Liberdade e Bem-estar Financeiro</p>
      </header>
      
      <div className="grid md:grid-cols-3 gap-6">
        {/* Coluna de dados pessoais */}
        <div className="bg-white rounded-lg shadow p-4">
          <h2 className="text-xl font-semibold text-indigo-700 mb-4">Dados Pessoais</h2>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Idade Atual</label>
            <input 
              type="number" 
              className="w-full p-2 border border-gray-300 rounded"
              value={idade}
              onChange={(e) => setIdade(Number(e.target.value))}
            />
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Expectativa de Vida</label>
            <input 
              type="number" 
              className="w-full p-2 border border-gray-300 rounded"
              value={expectativaVida}
              onChange={(e) => setExpectativaVida(Number(e.target.value))}
            />
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Renda Mensal Atual (R$)</label>
            <input 
              type="number" 
              className="w-full p-2 border border-gray-300 rounded"
              value={rendaMensal}
              onChange={(e) => setRendaMensal(Number(e.target.value))}
            />
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Patrimônio Financeiro Atual (R$)</label>
            <input 
              type="number" 
              className="w-full p-2 border border-gray-300 rounded"
              value={patrimonioFinanceiro}
              onChange={(e) => setPatrimonioFinanceiro(Number(e.target.value))}
            />
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Patrimônio em Imóveis Atual (R$)</label>
            <input 
              type="number" 
              className="w-full p-2 border border-gray-300 rounded"
              value={patrimonioImoveis}
              onChange={(e) => setPatrimonioImoveis(Number(e.target.value))}
            />
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Aporte Mensal (R$)</label>
            <input 
              type="number" 
              className="w-full p-2 border border-gray-300 rounded"
              value={aporteRecorrente}
              onChange={(e) => setAporteRecorrente(Number(e.target.value))}
            />
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Idade Meta para Independência</label>
            <input 
              type="number" 
              className="w-full p-2 border border-gray-300 rounded"
              value={idadeMeta}
              onChange={(e) => setIdadeMeta(Number(e.target.value))}
            />
          </div>
        </div>
        
        {/* Coluna de cenários econômicos */}
        <div className="bg-white rounded-lg shadow p-4">
          <h2 className="text-xl font-semibold text-indigo-700 mb-4">Horizontes Possíveis</h2>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Cenário</label>
            <div className="grid grid-cols-4 gap-2">
              <button 
                className={`p-2 text-sm rounded ${cenarioAtual === 'base' ? 'bg-indigo-600 text-white' : 'bg-gray-200 text-gray-700'}`}
                onClick={() => aplicarCenario('base')}
              >
                Base
              </button>
              <button 
                className={`p-2 text-sm rounded ${cenarioAtual === 'otimista' ? 'bg-indigo-600 text-white' : 'bg-gray-200 text-gray-700'}`}
                onClick={() => aplicarCenario('otimista')}
              >
                Otimista
              </button>
              <button 
                className={`p-2 text-sm rounded ${cenarioAtual === 'pessimista' ? 'bg-indigo-600 text-white' : 'bg-gray-200 text-gray-700'}`}
                onClick={() => aplicarCenario('pessimista')}
              >
                Desafiador
              </button>
              <button 
                className={`p-2 text-sm rounded ${cenarioAtual === 'personalizado' ? 'bg-indigo-600 text-white' : 'bg-gray-200 text-gray-700'}`}
                onClick={() => setCenarioAtual('personalizado')}
              >
                Personal.
              </button>
            </div>
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Retorno de Investimentos Financeiros (% a.a.)</label>
            <input 
              type="number" 
              step="0.1"
              className="w-full p-2 border border-gray-300 rounded"
              value={retornoInvestimento}
              onChange={(e) => setRetornoInvestimento(Number(e.target.value))}
            />
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Valorização de Imóveis (% a.a.)</label>
            <input 
              type="number" 
              step="0.1"
              className="w-full p-2 border border-gray-300 rounded"
              value={retornoImoveis}
              onChange={(e) => setRetornoImoveis(Number(e.target.value))}
            />
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Inflação Projetada (% a.a.)</label>
            <input 
              type="number" 
              step="0.1"
              className="w-full p-2 border border-gray-300 rounded"
              value={inflacao}
              onChange={(e) => setInflacao(Number(e.target.value))}
            />
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Nível de Estilo de Vida (%)</label>
            <input 
              type="number" 
              step="1"
              className="w-full p-2 border border-gray-300 rounded"
              value={nivelEstiloVida}
              onChange={(e) => setNivelEstiloVida(Number(e.target.value))}
            />
          </div>
          
          <button 
            className="mt-2 bg-indigo-600 text-white p-2 rounded w-full"
            onClick={salvarCenario}
          >
            Salvar Cenário Personalizado
          </button>
        </div>
        
        {/* Coluna de perfil emocional */}
        <div className="bg-white rounded-lg shadow p-4">
          <h2 className="text-xl font-semibold text-indigo-700 mb-4">Mindset Financeiro</h2>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Conforto com Decisões Financeiras (1-10)</label>
            <div className="flex items-center">
              <span className="text-sm text-gray-500 mr-2">Baixo</span>
              <input 
                type="range" 
                min="1" 
                max="10" 
                className="w-full"
                value={confortoDecisoes}
                onChange={(e) => setConfortoDecisoes(Number(e.target.value))}
              />
              <span className="text-sm text-gray-500 ml-2">Alto</span>
            </div>
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Importância da Tranquilidade Financeira (1-10)</label>
            <div className="flex items-center">
              <span className="text-sm text-gray-500 mr-2">Baixa</span>
              <input 
                type="range" 
                min="1" 
                max="10" 
                className="w-full"
                value={importanciaSeguranca}
                onChange={(e) => setImportanciaSeguranca(Number(e.target.value))}
              />
              <span className="text-sm text-gray-500 ml-2">Alta</span>
            </div>
          </div>
          
          <div className="mb-3">
            <label className="block text-gray-700 text-sm font-bold mb-1">Tolerância a Riscos Financeiros (1-10)</label>
            <div className="flex items-center">
              <span className="text-sm text-gray-500 mr-2">Baixa</span>
              <input 
                type="range" 
                min="1" 
                max="10" 
                className="w-full"
                value={toleranciaRisco}
                onChange={(e) => setToleranciaRisco(Number(e.target.value))}
              />
              <span className="text-sm text-gray-500 ml-2">Alta</span>
            </div>
          </div>
          
          <button 
            className="mt-4 bg-indigo-600 text-white p-2 rounded w-full"
            onClick={calcularPerfilEmocional}
          >
            Analisar Mindset Financeiro
          </button>
        </div>
      </div>
      
      {/* Seção de resultados */}
      <div className="mt-8 bg-white rounded-lg shadow p-4">
        <div className="flex justify-between items-center mb-4">
          <h2 className="text-xl font-semibold text-indigo-700">Mapa de Liberdade Financeira</h2>
          <button 
            className="bg-indigo-600 text-white px-4 py-2 rounded text-sm"
            onClick={exportarPDF}
          >
            Exportar PDF
          </button>
        </div>
        
        <div className="grid md:grid-cols-3 gap-4 mb-6">
          <div className="bg-gray-50 p-4 rounded border">
            <h3 className="text-lg font-semibold text-gray-700 mb-2">Renda na Independência</h3>
            <p className="text-2xl font-bold text-indigo-700">R$ {rendaIndependencia.toFixed(2)}</p>
            <p className="text-sm text-gray-500">por mês ({nivelEstiloVida}% da renda atual)</p>
          </div>
          
          <div className="bg-gray-50 p-4 rounded border">
            <h3 className="text-lg font-semibold text-gray-700 mb-2">Horizonte de Segurança</h3>
            <p className="text-2xl font-bold text-indigo-700">{horizonteSeguranca} anos</p>
            <p className="text-sm text-gray-500">dos {expectativaVida - idadeMeta} anos de independência</p>
          </div>
          
          <div className="bg-gray-50 p-4 rounded border" style={{borderColor: mapearCorScore(scoreLiberdade)}}>
            <h3 className="text-lg font-semibold text-gray-700 mb-2">Score de Liberdade</h3>
            <p className="text-2xl font-bold" style={{color: mapearCorScore(scoreLiberdade)}}>{scoreLiberdade}%</p>
            <p className="text-sm text-gray-500">Cobertura da jornada de independência</p>
          </div>
        </div>
        
        {/* Gráfico de projeção */}
        <div className="h-64 mb-6">
          <ResponsiveContainer width="100%" height="100%">
            <AreaChart
              data={dadosProjecao}
              margin={{ top: 5, right: 30, left: 20, bottom: 5 }}
            >
              <CartesianGrid strokeDasharray="3 3" />
              <XAxis dataKey="idade" label={{ value: 'Idade', position: 'insideBottomRight', offset: -5 }} />
              <YAxis />
              <Tooltip formatter={(value) => `R$ ${Number(value).toLocaleString('pt-BR')}`} />
              <Legend />
              <ReferenceLine x={idadeMeta} stroke="#FF0000" label="Independência" />
              <Area type="monotone" dataKey="patrimonioFinanceiro" name="Patrimônio Financeiro" stroke="#82ca9d" fill="#82ca9d" stackId="1" />
              <Area type="monotone" dataKey="patrimonioImoveis" name="Patrimônio Imóveis" stroke="#8884d8" fill="#8884d8" stackId="1" />
            </AreaChart>
          </ResponsiveContainer>
        </div>
        
        {/* Resultado emocional */}
        {mostrarResultadoEmocional && (
          <div className="mt-4 p-4 bg-indigo-50 rounded-lg border border-indigo-200">
            <h3 className="text-lg font-semibold text-indigo-700 mb-2">Análise de Bem-estar Financeiro</h3>
            <p className="text-gray-700">{obterTextoAnaliseEmocional()}</p>
            
            {scoreLiberdade < 100 && (
              <div className="mt-4">
                <h4 className="font-semibold text-indigo-700 mb-2">Estratégias para impulsionar sua liberdade financeira:</h4>
                <ul className="list-disc pl-5 text-gray-700">
                  <li>Aumentar seu aporte mensal para R$ {(aporteRecorrente * 1.5).toFixed(2)} pode melhorar significativamente seu score de liberdade.</li>
                  {idadeMeta < 65 && (
                    <li>Considerar estender sua fase de acumulação em alguns anos pode diminuir o período de desacumulação e aumentar seu patrimônio.</li>
                  )}
                  <li>Revisar sua estratégia de investimentos para buscar um retorno real mais elevado, alinhado com seu perfil de tolerância a risco.</li>
                  <li>Considerar um nível de estilo de vida um pouco menor (entre 70-75%) pode fazer seu patrimônio durar por mais tempo.</li>
                </ul>
              </div>
            )}
          </div>
        )}
      </div>
    </div>
  );
};

export default JornadaFinanceira360;
