<div class="box box-key-concept"><p class="box-title"><strong>PHASE 2: Independent Programs on Quantum Computer</strong></p><p>Experiments 12–15 | Qiskit Programming on IBM Hardware</p></div><table>
<colgroup>
<col style="width: 12%"/>
<col style="width: 87%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>EXP</strong></p>
<p><strong>12</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Bernstein-Vazirani Algorithm — Hidden String Recovery</strong></p>
<p>Phase 2 | Own Code | Sem III</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Implement the Bernstein-Vazirani algorithm to find a hidden n-bit string s from f(x)=s·x mod 2 using exactly one quantum query, compared to n classical queries.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>Phase 2 | Own Code | Sem III</td>
</tr>
</tbody>
</table><h1 id="experiment-12-bernstein-vazirani-algorithm-hidden-string-recovery"> Experiment 12: Bernstein-Vazirani Algorithm — Hidden String Recovery</h1><h2 id="background-theory-11">1. Background Theory</h2><p>The Bernstein-Vazirani (BV) algorithm: given oracle for <img loading="lazy" src="content/images/image59.png"/> (inner product mod 2 with hidden string s ∈ {0,1}ⁿ), find s with exactly 1 quantum query vs n classical queries. Circuit: Hⁿ|0ⁿ⟩|1⟩ → oracle U_f → Hⁿ → measure → output is s exactly. Phase kickback: oracle applies (-1)^{s·x} phases, and Hⁿ then disentangles all bits of s simultaneously via interference. Oracle implementation: CX from input qubit i to ancilla for each i where s_i=1.</p><h2 id="qiskit-code-10">2. Qiskit Code</h2><h3 id="first-program-simple-version-11">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># Experiment 12 — First Program: Bernstein-Vazirani Algorithm — Hidden String Recovery
# Phase 2 | Dr. S. K. Jain, Invertis University, India
# Bernstein-Vazirani Algorithm — Hidden String Recovery
# ----------------------------------------------------------------------------
from qiskit import QuantumCircuit, transpile
from qiskit_aer import AerSimulator
s = '1011' # hidden string to recover
n = len(s)
qc = QuantumCircuit(n + 1, n)
qc.x(n); qc.h(n) # ancilla in |1&gt;, then Hadamard -&gt; |-&gt;
qc.h(range(n)) # Hadamard on all input qubits
for i, bit in enumerate(reversed(s)): # oracle: CX(qi, ancilla) where s_i = 1
if bit == '1':
qc.cx(i, n)
qc.h(range(n))
qc.measure(range(n), range(n))
sim = AerSimulator()
counts = sim.run(transpile(qc, sim), shots=1024).result().get_counts()
recovered = max(counts, key=counts.get)
print(f'Hidden string s = {s}')
print(f'Recovered string = {recovered}')
print(f'Match = {recovered == s}')
print(f'Classical queries needed = {n} (one bit at a time)')
print(f'Quantum queries needed = 1 (this single circuit run)')</code></pre><p><strong>Expected Output:</strong></p><div class="box box-generic"><p>▶ Exp 12 — Expected Output &amp; Console Results</p><p>Hidden string s = 1011</p><p>Recovered string = 1011</p><p>Match = True</p><p>Classical queries needed = 4 (one bit at a time)</p><p>Quantum queries needed = 1 (this single circuit run)</p></div><h3 id="full-program-complete-version-10">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ───────────────────────────────────────────────────────────────────────
# Experiment 12: Bernstein-Vazirani Algorithm — Hidden String Recovery
# Phase 2 | Dr. S. K. Jain, Invertis University, India
# ───────────────────────────────────────────────────────────────────────
from qiskit import QuantumCircuit, transpile
from qiskit_aer import AerSimulator
from qiskit_aer.noise import NoiseModel, depolarizing_error
import numpy as np, matplotlib.pyplot as plt
n = 4
sim = AerSimulator()
def bv_circuit(s):
qc = QuantumCircuit(n + 1, n)
qc.x(n); qc.h(n)
qc.h(range(n))
for i, bit in enumerate(reversed(s)):
if bit == '1':
qc.cx(i, n)
qc.h(range(n))
qc.measure(range(n), range(n))
return qc
# ---- Part 1: exhaustive test over all 2^n hidden strings ------------------
all_strings = [format(k, f'0{n}b') for k in range(2**n)]
success = {}
for s in all_strings:
qc = bv_circuit(s)
counts = sim.run(transpile(qc, sim), shots=1024).result().get_counts()
recovered = max(counts, key=counts.get)
success[s] = counts.get(s, 0) / 1024
print(f's={s} recovered={recovered} P(correct)={success[s]:.4f} match={recovered==s}')
overall_success_rate = np.mean(list(success.values()))
print(f'\nOverall mean success probability across all {2**n} strings: {overall_success_rate:.4f}')
# ---- Part 2: classical vs quantum query complexity -------------------------
print(f'Classical queries required: {n}')
print(f'Quantum queries required: 1')
# ---- Part 3: robustness under depolarising noise ----------------------------
noise_model = NoiseModel()
noise_model.add_all_qubit_quantum_error(depolarizing_error(0.01, 1), ['h', 'x'])
noise_model.add_all_qubit_quantum_error(depolarizing_error(0.02, 2), ['cx'])
sim_noisy = AerSimulator(noise_model=noise_model)
s_test = '1011'
qc = bv_circuit(s_test)
counts_noisy = sim_noisy.run(transpile(qc, sim_noisy), shots=4096).result().get_counts()
p_noisy = counts_noisy.get(s_test, 0) / 4096
print(f"\nUnder depolarising noise (p1=0.01, p2=0.02): P(correct s='{s_test}') = {p_noisy:.4f}")
# ---- Part 4: circuit resource analysis --------------------------------------
tqc = transpile(bv_circuit(s_test), sim, optimization_level=1)
print(f'Circuit depth = {tqc.depth()}, gate count = {tqc.size()}')
# ---- Visualisation -----------------------------------------------------------
fig, axes = plt.subplots(1, 2, figsize=(13, 5))
axes[0].bar(success.keys(), success.values(), color='#4C72B0')
axes[0].set_xticklabels(success.keys(), rotation=90, fontsize=6)
axes[0].axhline(1.0, color='green', linestyle='--')
axes[0].set_title('BV Success Probability for Every 4-bit Hidden String')
axes[0].set_ylabel('P(recovered = s)')
axes[1].bar(['Classical\n(n queries)', 'Quantum\n(1 query)', 'Quantum + noise\n(1 query)'],
[1.0, 1.0, p_noisy], color=['#C44E52', '#55A868', '#DD8452'])
axes[1].set_ylim(0, 1.1)
axes[1].set_title('Ideal vs Noisy Success Probability')
plt.tight_layout()
plt.savefig('lab12_bv_full_analysis.png', dpi=150)
plt.show()</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image60.png"/></figure><div class="box box-generic"><p>▶ Exp 12 — Expected Output &amp; Console Results</p><p>s=0000 recovered=0000 P(correct)=1.0000 match=True</p><p>s=0001 recovered=0001 P(correct)=1.0000 match=True</p><p>s=0010 recovered=0010 P(correct)=1.0000 match=True</p><p>... (all 16 hidden strings recovered correctly)</p><p>s=1111 recovered=1111 P(correct)=1.0000 match=True</p><p>Overall mean success probability across all 16 strings: 1.0000</p><p>Classical queries required: 4 Quantum queries required: 1</p><p>Under depolarising noise (p1=0.01, p2=0.02): P(correct s='1011') = 0.9197</p><p>Circuit depth = 6, gate count = 14</p></div><h2 id="observation-and-results-11">3. Observation and Results</h2><h3 id="observation-tables-4">Observation Tables</h3><p><em>Complete the observation tables for Experiment 12. Fill in during the practical session using actual experimental data from AQLL and your own program.</em></p><table>
<colgroup>
<col style="width: 27%"/>
<col style="width: 17%"/>
<col style="width: 19%"/>
<col style="width: 19%"/>
<col style="width: 16%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Parameter / Quantity</strong></td>
<td><strong>Expected Value</strong></td>
<td><strong>Measured Value (Simulation)</strong></td>
<td><strong>Measured Value (AQLL)</strong></td>
<td><strong>Notes</strong></td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><h2 id="discussion-questions-11">4. Discussion Questions</h2><ul>
<li><p>Why does the Bernstein-Vazirani algorithm need only a single oracle query while the classical algorithm needs n queries? Where exactly does this speed-up come from in the circuit?</p></li>
<li><p>What would happen to the measured output if the oracle instead implemented f(x) = s·x + 1 mod 2 (i.e. with a constant offset added)?</p></li>
<li><p>How does the phase-kickback trick used in this experiment relate to the phase oracle used in Grover's algorithm (Experiment 10)?</p></li>
<li><p>Write complete answers in your lab record with supporting calculations and diagrams.</p></li>
<li><p>For Phase 2: document your design choices and compare results with theoretical predictions.</p></li>
</ul><h2 id="lab-record-requirements-11">5. Lab Record Requirements</h2><ul>
<li><p>Run your own Qiskit program for Experiment 12. Document all design choices.</p></li>
<li><p>Run the First Program. Copy output to lab record.</p></li>
<li><p>Run the Full Program. Save all generated figures.</p></li>
<li><p>Complete all observation tables during the practical session.</p></li>
<li><p>Write complete answers to all Discussion Questions.</p></li>
</ul><table>
<colgroup>
<col style="width: 6%"/>
<col style="width: 93%"/>
</colgroup>
<tbody>
<tr class="odd">
<td colspan="2"><strong>⭐ VIVA VOCE QUESTIONS &amp; ANSWERS</strong></td>
</tr>
<tr class="even">
<td><strong>Q1</strong></td>
<td><strong>What are the key quantum concepts demonstrated in Experiment 12?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Experiment 12 demonstrates: The Bernstein-Vazirani (BV) algorithm: given oracle for f(x) = s·x mod 2 (inner product mod 2 with hidden string s ∈ {0,1}ⁿ), find s with exactly 1 quantum query vs n classical queries. Circuit: Hⁿ|0ⁿ⟩|1⟩ → oracle U_f → Hⁿ → measure → output is s exactly. Phase kickback: oracle applies (-1)^{s·x} ph... Students should understand both the theoretical foundations and the practical Qiskit implementation, and be able to explain the significance of each result in terms of quantum information science principles covered in this manual.</td>
</tr>
<tr class="even">
<td><strong>Q2</strong></td>
<td><strong>How do you interpret the simulation vs hardware results for this experiment?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Ideal simulation (Qiskit-Aer, no noise model) gives theoretically perfect results. IBM hardware introduces gate errors (~0.01-1% per gate), readout errors (~1-5%), T₁ relaxation, T₂ dephasing, and crosstalk. The difference quantifies total experimental error. Always compare with calibration data from backend.properties() to attribute errors to specific sources.</td>
</tr>
<tr class="even">
<td><strong>Q3</strong></td>
<td><strong>What modifications would improve fidelity for this experiment on IBM hardware?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Improvements: (1) Select qubits with lowest CX error using backend.properties(). (2) Use optimization_level=3 to minimise circuit depth. (3) Apply readout error mitigation. (4) Apply ZNE with gate folding. (5) Increase shot count. (6) Run multiple repetitions and average.</td>
</tr>
<tr class="even">
<td><strong>Q4</strong></td>
<td><strong>How does the Bernstein-Vazirani circuit structurally resemble the Deutsch-Jozsa algorithm, and what is the key difference in what each solves?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Both algorithms use the same Hⁿ–oracle–Hⁿ structure with an ancilla prepared in |−⟩ to exploit phase kickback. Deutsch-Jozsa distinguishes constant from balanced functions with a single query, whereas Bernstein-Vazirani solves the more specific problem of recovering an entire hidden bit-string s exactly, also in a single query.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>Why is the ancilla qubit prepared in the state (|0⟩−|1⟩)/√2 rather than a computational basis state?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Preparing the ancilla in |−⟩ makes the oracle's phase-flip action on the ancilla show up as an overall phase (-1)^{f(x)} on the input register, via the phase kickback mechanism — this is what allows a classically 'black-box' oracle query to be converted into an observable interference pattern on the input qubits.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>What would happen if the oracle were implemented incorrectly, using a CX from the ancilla to the input qubits instead of the other way around?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The circuit would no longer correctly implement the oracle function f(x)=s·x mod 2 as intended, and the Hⁿ interference pattern used to recover s would not correspond to the desired dot product, causing the algorithm to output an incorrect (or meaningless) recovered string rather than the true hidden string s.</td>
</tr>
</tbody>
</table><table>
<colgroup>
<col style="width: 12%"/>
<col style="width: 87%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><p><strong>EXP</strong></p>
<p><strong>13</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Quantum Random Number Generator — Statistical Testing</strong></p>
<p>Phase 2 | Own Code | IBM Hardware | Sem III</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Exploit fundamental quantum measurement randomness to generate true random numbers; perform chi-squared and autocorrelation statistical tests; compare simulation vs IBM hardware.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>Phase 2 | Own Code | IBM Hardware | Sem III</td>
</tr>
</tbody>
</table>