<h1 id="experiment-14-bell-states-and-chsh-inequality-full-ibm-hardware-test">Experiment 14: Bell States and CHSH Inequality — Full IBM Hardware Test</h1><h2 id="background-theory-13">1. Background Theory</h2><p>Four Bell states: <img loading="lazy" src="content/images/image35.png"/> (H·CX), <img loading="lazy" src="content/images/image63.png"/> (H·CX·Z), <img loading="lazy" src="content/images/image64.png"/> (H·CX·X), <img loading="lazy" src="content/images/image65.png"/> (H·CX·X·Z). All have concurrence=1, purity=1. CHSH optimal angles: a=0°, a’=90°, b=45°, b’=135°. On IBM hardware: |S| ≈ 2.2-2.7 (below Tsirelson 2√2 due to noise).</p><h2 id="qiskit-code-12">2. Qiskit Code</h2><h3 id="first-program-simple-version-13">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># Experiment 14 — First Program: Bell States and CHSH Inequality — Full IBM Hardware Test
# Phase 2 | Dr. S. K. Jain, Invertis University, India
# Bell States and CHSH Inequality — Full IBM Hardware Test
# ------------------------------------------------------------------------
from qiskit import QuantumCircuit, transpile
from qiskit.quantum_info import Statevector
from qiskit_aer import AerSimulator
import numpy as np
# ---- Part 1: prepare and verify the four Bell states -----------------------
def bell_circuit(kind):
qc = QuantumCircuit(2)
qc.h(0); qc.cx(0, 1)
if kind in ('Phi-', 'Psi-'):
qc.z(0)
if kind in ('Psi+', 'Psi-'):
qc.x(1)
return qc
print('Bell state verification:')
for kind in ['Phi+', 'Phi-', 'Psi+', 'Psi-']:
sv = Statevector(bell_circuit(kind))
print(f' |{kind}&gt;: purity={sv.purity().real:.4f} amplitudes={np.round(sv.data, 3)}')
# ---- Part 2: CHSH measurement circuit at optimal angles --------------------
a, ap, b, bp = 0, np.pi/2, np.pi/4, 3*np.pi/4 # optimal CHSH angles
def chsh_circuit(angle_a, angle_b):
qc = QuantumCircuit(2, 2)
qc.h(0); qc.cx(0, 1)
qc.ry(-angle_a, 0)
qc.ry(-angle_b, 1)
qc.measure([0, 1], [0, 1])
return qc
sim = AerSimulator()
def correlation(angle_a, angle_b, shots=4096):
counts = sim.run(transpile(chsh_circuit(angle_a, angle_b), sim), shots=shots).result().get_counts()
return sum((1 if k[0] == k[1] else -1) * v for k, v in counts.items()) / shots
E_ab, E_abp, E_apb, E_apbp = correlation(a, b), correlation(a, bp), correlation(ap, b), correlation(ap, bp)
S = abs(E_ab - E_abp + E_apb + E_apbp)
print(f'\nE(a,b) = {E_ab:+.4f}')
print(f"E(a,b') = {E_abp:+.4f}")
print(f"E(a',b) = {E_apb:+.4f}")
print(f"E(a',b') = {E_apbp:+.4f}")
print(f'\nCHSH |S| = {S:.4f} (classical limit=2, quantum max 2*sqrt(2)={2*np.sqrt(2):.4f})')
print(f'Bell inequality violated: {S &gt; 2}')</code></pre><p><strong>Expected Output:</strong></p><div class="box box-generic"><p>▶ Exp 14 — Expected Output &amp; Console Results</p><p>Bell state verification:</p><p>|Phi+&gt;: purity=1.0000 amplitudes=[0.707+0.j 0.+0.j 0.+0.j 0.707+0.j]</p><p>|Phi-&gt;: purity=1.0000 amplitudes=[0.707+0.j -0.+0.j 0.+0.j -0.707+0.j]</p><p>|Psi+&gt;: purity=1.0000 amplitudes=[0.+0.j 0.707+0.j 0.707+0.j 0.+0.j]</p><p>|Psi-&gt;: purity=1.0000 amplitudes=[0.+0.j -0.707+0.j 0.707+0.j -0.+0.j]</p><p>E(a,b) = +0.7139</p><p>E(a,b') = -0.7031</p><p>E(a',b) = +0.7041</p><p>E(a',b') = +0.7041</p><p>CHSH |S| = 2.8252 (classical limit=2, quantum max 2*sqrt(2)=2.8284)</p><p>Bell inequality violated: True</p></div><h3 id="full-program-complete-version-12">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ───────────────────────────────────────────────────────────────────────
# Experiment 14: Bell States and CHSH Inequality — Full IBM Hardware Test
# Phase 2 | Dr. S. K. Jain, Invertis University, India
# ───────────────────────────────────────────────────────────────────────
from qiskit import QuantumCircuit, transpile
from qiskit.quantum_info import Statevector
from qiskit_aer import AerSimulator
from qiskit_ibm_runtime import QiskitRuntimeService, SamplerV2 as Sampler
import numpy as np, matplotlib.pyplot as plt
def bell_circuit(kind):
qc = QuantumCircuit(2)
qc.h(0); qc.cx(0, 1)
if kind in ('Phi-', 'Psi-'):
qc.z(0)
if kind in ('Psi+', 'Psi-'):
qc.x(1)
return qc
def chsh_circuit(angle_a, angle_b):
qc = QuantumCircuit(2, 2)
qc.h(0); qc.cx(0, 1)
qc.ry(-angle_a, 0)
qc.ry(-angle_b, 1)
qc.measure([0, 1], [0, 1])
return qc
def correlation_from_counts(counts, shots):
return sum((1 if k[0] == k[1] else -1) * v for k, v in counts.items()) / shots
a, ap, b, bp = 0, np.pi/2, np.pi/4, 3*np.pi/4
angle_pairs = {'E(a,b)': (a, b), "E(a,b')": (a, bp), "E(a',b)": (ap, b), "E(a',b')": (ap, bp)}
# ---- Part 1: ideal simulation baseline --------------------------------------
sim = AerSimulator()
sim_E = {}
for label, (aa, bb) in angle_pairs.items():
counts = sim.run(transpile(chsh_circuit(aa, bb), sim), shots=4096).result().get_counts()
sim_E[label] = correlation_from_counts(counts, 4096)
S_sim = abs(sim_E['E(a,b)'] - sim_E["E(a,b')"] + sim_E["E(a',b)"] + sim_E["E(a',b')"])
print(f'Simulation |S| = {S_sim:.4f}')
# ---- Part 2: submit the same circuits to real IBM Quantum hardware ---------
try:
service = QiskitRuntimeService()
backend = service.least_busy(operational=True, simulator=False)
print(f'\nSelected backend: {backend.name} ({backend.num_qubits} qubits)')
hw_circuits = [transpile(chsh_circuit(aa, bb), backend, optimization_level=3)
for aa, bb in angle_pairs.values()]
sampler = Sampler(backend)
job = sampler.run(hw_circuits, shots=4096)
print(f'IBM Quantum Job ID: {job.job_id()}')
job_result = job.result()
hw_E = {}
for label, res in zip(angle_pairs, job_result):
counts = res.data.c.get_counts()
hw_E[label] = correlation_from_counts(counts, 4096)
S_hw = abs(hw_E['E(a,b)'] - hw_E["E(a,b')"] + hw_E["E(a',b)"] + hw_E["E(a',b')"])
print(f'Hardware |S| = {S_hw:.4f}')
except Exception as exc:
print(f'\n[IBM Quantum hardware not reachable in this session: {exc}]')
print('Record the hardware |S| value from your Runtime job in the Observation Table.')
S_hw = None
# ---- Part 3: verify all four Bell states via statevector purity ------------
print('\nBell state verification:')
purities = {}
for kind in ['Phi+', 'Phi-', 'Psi+', 'Psi-']:
sv = Statevector(bell_circuit(kind))
purities[kind] = sv.purity().real
print(f' |{kind}&gt;: purity={purities[kind]:.4f}')
# ---- Visualisation -----------------------------------------------------------
fig, axes = plt.subplots(1, 2, figsize=(12, 5))
axes[0].bar(purities.keys(), purities.values(), color='#4C72B0')
axes[0].set_ylim(0, 1.1)
axes[0].set_title('Purity of Each Bell State (ideal = 1.0)')
labels = ['Simulation', 'Hardware'] if S_hw is not None else ['Simulation']
values = [S_sim, S_hw] if S_hw is not None else [S_sim]
axes[1].bar(labels, values, color=['#55A868', '#C44E52'][:len(values)])
axes[1].axhline(2.0, color='black', linestyle='--', label='Classical limit')
axes[1].axhline(2*np.sqrt(2), color='blue', linestyle=':', label='Tsirelson bound')
axes[1].set_title('CHSH |S|: Simulation vs Hardware')
axes[1].legend(fontsize=8)
plt.tight_layout()
plt.savefig('lab14_bell_chsh_full_analysis.png', dpi=150)
plt.show()</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image66.png"/></figure><div class="box box-generic"><p>▶ Exp 14 — Expected Output &amp; Console Results</p><p>Simulation |S| = 2.8535</p><p>[IBM Quantum hardware not reachable in this session: Unable to find</p><p>account. Please make sure an account with the channel name</p><p>'ibm_quantum_platform' is saved.]</p><p>Record the hardware |S| value from your Runtime job in the Observation Table.</p><p>Bell state verification:</p><p>|Phi+&gt;: purity=1.0000 |Phi-&gt;: purity=1.0000</p><p>|Psi+&gt;: purity=1.0000 |Psi-&gt;: purity=1.0000</p><p>NOTE: with a saved IBM Quantum account, a second 'Hardware' bar appears</p><p>in the right-hand panel below, typically |S| ~ 2.2-2.7 (noise-reduced).</p></div><h2 id="observation-and-results-13">3. Observation and Results</h2><h3 id="observation-tables-6">Observation Tables</h3><p><em>Complete the observation tables for Experiment 14. Fill in during the practical session using actual experimental data from AQLL and your own program.</em></p><table>
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
</table><h3 id="ibm-hardware-execution-record-1">IBM Hardware Execution Record</h3><table>
<colgroup>
<col style="width: 38%"/>
<col style="width: 61%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Parameter</strong></td>
<td><strong>Value Recorded</strong></td>
</tr>
<tr class="even">
<td>Device Name</td>
<td></td>
</tr>
<tr class="odd">
<td>Number of Qubits</td>
<td></td>
</tr>
<tr class="even">
<td>Qubits Used</td>
<td></td>
</tr>
<tr class="odd">
<td>T₁ (μs) per qubit</td>
<td></td>
</tr>
<tr class="even">
<td>T₂ (μs) per qubit</td>
<td></td>
</tr>
<tr class="odd">
<td>Gate Error Rate</td>
<td></td>
</tr>
<tr class="even">
<td>Readout Error</td>
<td></td>
</tr>
<tr class="odd">
<td>IBM Quantum Job ID</td>
<td></td>
</tr>
<tr class="even">
<td>Submission Date/Time</td>
<td></td>
</tr>
<tr class="odd">
<td>Queue Wait Time</td>
<td></td>
</tr>
<tr class="even">
<td>Hardware Result</td>
<td></td>
</tr>
<tr class="odd">
<td>Simulation Result</td>
<td></td>
</tr>
<tr class="even">
<td>Error Rate (%)</td>
<td></td>
</tr>
</tbody>
</table><h2 id="discussion-questions-13">4. Discussion Questions</h2><ul>
<li><p>All four Bell states have identical purity and concurrence — what physically distinguishes them from one another?</p></li>
<li><p>Why does |S| measured on real IBM hardware typically fall between the classical bound of 2 and the Tsirelson bound of 2√2, rather than reaching 2√2 exactly?</p></li>
<li><p>Which of the four Bell states would be easiest to distinguish from the others using only Z-basis measurements, and why?</p></li>
<li><p>Write complete answers in your lab record with supporting calculations and diagrams.</p></li>
<li><p>For Phase 2: document your design choices and compare results with theoretical predictions.</p></li>
</ul><h2 id="lab-record-requirements-13">5. Lab Record Requirements</h2><ul>
<li><p>Run your own Qiskit program for Experiment 14. Document all design choices.</p></li>
<li><p>Run the First Program. Copy output to lab record.</p></li>
<li><p>Run the Full Program. Save all generated figures.</p></li>
<li><p>Complete all observation tables during the practical session.</p></li>
<li><p>Record IBM Quantum Job ID immediately after submission.</p></li>
<li><p>Compare hardware vs simulation results. Calculate error rate.</p></li>
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
<td><strong>What are the key quantum concepts demonstrated in Experiment 14?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Experiment 14 demonstrates: Four Bell states: |Φ+⟩=(|00⟩+|11⟩)/√2 (H·CX), |Φ-⟩=(|00⟩-|11⟩)/√2 (H·CX·Z), |Ψ+⟩=(|01⟩+|10⟩)/√2 (H·CX·X), |Ψ-⟩=(|01⟩-|10⟩)/√2 (H·CX·X·Z). All have concurrence=1, purity=1. CHSH optimal angles: a=0°, a’=90°, b=45°, b’=135°. On IBM hardware: |S| ≈ 2.2-2.7 (below Tsirelson 2√2 due to noise).... Students should understand both the theoretical foundations and the practical Qiskit implementation, and be able to explain the significance of each result in terms of quantum information science principles covered in this manual.</td>
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
<td><strong>Why do all four Bell states have concurrence exactly 1, and what does this tell us about their entanglement?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Concurrence measures the degree of two-qubit entanglement, ranging from 0 (separable) to 1 (maximally entangled). All four Bell states are maximally entangled pure states related to each other only by local Pauli operations (X and/or Z on one qubit), which do not change the amount of entanglement, so all four share concurrence 1.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>How would you experimentally verify which of the four Bell states was actually prepared, given that Z-basis measurements alone give the same outcome statistics for |Φ⁺⟩ and |Φ⁻⟩?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Since |Φ⁺⟩ and |Φ⁻⟩ differ only by a relative phase (a Z-type difference) invisible to a computational-basis measurement, distinguishing them requires measuring in a complementary basis such as X, or performing a full Bell-state discrimination circuit (CNOT + Hadamard before measurement) that maps each of the four Bell states to a distinct computational basis outcome.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>Why does running this experiment on real IBM hardware consistently give a lower |S| than the ideal simulation, and is this gap expected to shrink over time?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Real hardware introduces gate errors, decoherence (T1/T2), and readout errors that are absent in an ideal noiseless simulator, all of which reduce the measured correlations below their theoretical values. As hardware fidelities continue to improve generation over generation, this gap is expected to shrink, though it is unlikely to vanish completely given fundamental physical noise sources.</td>
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
<p><strong>15</strong></p>
<p>3 hrs</p></td>
<td><p><strong>IBM Quantum Hardware — Complete Circuit Execution</strong></p>
<p>Phase 2 | IBM Hardware | Sem III (Capstone)</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Submit a quantum circuit to real IBM Quantum hardware; compare ideal simulation vs noisy hardware; read calibration data; perform noise-aware transpilation at optimisation level 3; analyse the discrepancy.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>Phase 2 | IBM Hardware | Sem III (Capstone)</td>
</tr>
</tbody>
</table>