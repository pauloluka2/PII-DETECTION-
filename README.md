# 🛡️ PII Detection: Detecção de Informações de Identificação Pessoal

**Tipo:** Pesquisa e Avaliação de Modelos  
**Disciplina:** Machine Learning II  
**Grupo:** Enzo Farias de Moraes, Luann Gagliardi e Paulo Henrique Tavares Delfino de Andrade  

---

## 📌 Sobre o Projeto

Este projeto tem como objetivo avaliar e comparar de forma rigorosa diferentes arquiteturas de modelos de Inteligência Artificial e abordagens baseadas em regras (Regex) para a identificação e anonimização de dados sensíveis em textos. O foco central é validar a aplicabilidade destas soluções em cenários reais, garantindo a conformidade com a Lei Geral de Proteção de Dados (LGPD).

## 🎯 Objetivos Específicos

* **Arquitetura Padronizada:** Implementar uma arquitetura de software Orientada a Objetos para facilitar o consumo de diferentes modelos fundacionais.
* **Avaliação de Modelos:** Comparar o desempenho de modelos pré-treinados (`RoBERTa`, `DeBERTa`, `XLM-RoBERTa`) em um benchmark utilizando um dataset sintético bilíngue (inglês e português).
* **Métrica Customizada:** Desenvolver um motor de avaliação focado no **F2-Score** para penalizar severamente os Falsos Negativos (vazamentos).
* **Aplicação Real:** Validar a utilidade prática dos modelos em um documento comercial (contrato de compra e venda) utilizando técnicas de *Span Merging* (mesclagem de recortes de texto).
* **Solução Definitiva:** Propor e construir um modelo híbrido (**Ensemble**) que une a flexibilidade semântica da IA com a precisão absoluta de expressões regulares.

---

## 🛠️ Arquitetura do Sistema e Metodologia

Para garantir a qualidade, manutenibilidade e escalabilidade do código, o pipeline adota o paradigma de Programação Orientada a Objetos (POO) e princípios de Polimorfismo.

1. **Classe Abstrata (`BasePIIDetector`):** Um contrato rígido utilizando o módulo nativo `abc`. Qualquer nova arquitetura precisa implementar um método de chamada padronizado, permitindo que a avaliação rode sem conhecer os detalhes internos do modelo.
2. **Extração Dinâmica de Entidades:** O código consulta o `AutoConfig` dos pesos originais e descobre automaticamente as *tags* de privacidade suportadas por cada modelo.
3. **Cenário *Out-of-Distribution*:** O sistema consome e extrai amostras rigorosamente isoladas por idioma do dataset `ai4privacy/pii-masking-openpii-1m`, operando de forma otimizada via *Lazy Loading*.

### Modelos NLP Avaliados
* **RoBERTa:** i2b2 - Médico EN
* **DeBERTa:** Compliance PII
* **XLM-RoBERTa:** Multilíngue (Melhor adaptação sintática ao idioma Português).

---

## 📊 Métrica Estratégica Orientada ao Risco (LGPD)

Em tarefas tradicionais de Machine Learning, métricas como o F1-Score são o padrão. No entanto, em proteção de dados, os erros têm pesos corporativos e legais muito diferentes:

* **Falso Positivo (FP):** A IA mascara uma palavra comum. *Impacto: Leve inconveniente operacional.*
* **Falso Negativo (FN):** A IA deixa passar um dado sensível real (ex: CPF). *Impacto: Quebra de conformidade, multa severa e risco jurídico.*

Para mitigar o vazamento, desenvolvemos o motor de testes com base no **F2-Score**, uma variação projetada para dar muito mais peso ao *Recall* (capacidade de varredura). A equação utilizada no projeto é:

$$F_2 = 5 \cdot \frac{\text{Precision} \cdot \text{Recall}}{(4 \cdot \text{Precision}) + \text{Recall}}$$

Dessa forma, o modelo é penalizado severamente caso ignore padrões brasileiros estruturados.

---

## 🏢 Cenário de Aplicação Real: Contratos Automotivos

Submetemos o pipeline a um contrato padrão de compra e venda de veículos. Para lidar com o desafio dos *Tokenizadores* (que frequentemente quebram palavras em múltiplos fragmentos menores), o projeto adota:

* **Aglutinação Dinâmica (*Span Merging*):** Identifica tokens vizinhos e os une em uma entidade contígua.
* **Match Rigoroso (Tolerância Zero):** Um mascaramento parcial (ex: esconder apenas o início de um CPF) é categorizado como vazamento total (Falso Negativo). A anonimização só é validada caso toda a string sensível seja ocultada perfeitamente.

---

## 🚀 Solução Final: Ensemble Híbrido

O estudo comprova que modelos puramente baseados em NLP não garantem blindagem completa em documentos burocráticos do Brasil, falhando ocasionalmente em padrões determinísticos complexos como RENAVAM ou PLACA.

A solução final contorna essa limitação através da arquitetura de **Ensemble Híbrido**:
1. **Varredura Semântica:** O `XLM-RoBERTa` percorre o texto extraindo contextos complexos que dependem de interpretação (ex: diferenciar o nome de uma pessoa de uma rua).
2. **Escudo Determinístico (Regex-BR):** Expressões regulares customizadas atacam impiedosamente metadados imutáveis (CPFs, CEPs, E-mails, Renavams).
3. **Fusão Limpa (*Cross-Merging*):** O algoritmo confronta ambas as predições. Se houver sobreposição, o sistema prioriza as extremidades mais abrangentes, gerando um mascaramento perfeito sem duplicações.

---

## 💻 Como Executar

**1. Instalação de Dependências**  
No seu terminal ou notebook, instale os pacotes base exigidos:

```bash
pip install transformers datasets pandas matplotlib -q
