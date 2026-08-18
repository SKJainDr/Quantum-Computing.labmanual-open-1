<h1 id="experiment-15-ibm-quantum-hardware-complete-circuit-execution">Experiment 15: IBM Quantum Hardware — Complete Circuit Execution</h1><h2 id="background-theory-14">1. Background Theory</h2><p>Capstone experiment: full IBM Quantum workflow. Steps: (1) Authenticate with QiskitRuntimeService, (2) Select backend with service.least_busy(), (3) Read calibration: T₁, T₂, gate errors, readout errors via backend.properties(), (4) Identify best qubit pair by minimum CX error, (5) Transpile with optimization_level=3 (maximum: KAK decomposition, Clifford simplification, noise-adaptive routing), (6) Submit job, record Job ID, (7) Retrieve and analyse results, (8) Calculate error rate = unexpected counts/total shots, (9) Discuss noise sources.</p><h2 id="qiskit-code-13">2. Qiskit Code</h2><h3 id="first-program-simple-version-14">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># ------------------------------------------------------------
# Experiment 15 — First Program: IBM Quantum Hardware — Complete Circuit Execution
# Dr. S. K. Jain, India
# ------------------------------------------------------------
from qiskit import QuantumCircuit, transpile
from qiskit_ibm_runtime import QiskitRuntimeService, SamplerV2 as Sampler
qc = QuantumCircuit(2, 2)
qc.h(0); qc.cx(0, 1)
qc.measure([0, 1], [0, 1])
try:
service = QiskitRuntimeService()
backend = service.least_busy(operational=True, simulator=False)
print(f'Selected backend: {backend.name}')
tqc = transpile(qc, backend, optimization_level=3)
sampler = Sampler(backend)
job = sampler.run([tqc], shots=1024)
print(f'Job ID: {job.job_id()}')
result = job.result()
counts = result[0].data.c.get_counts()
print(f'Hardware counts: {counts}')
except Exception as exc:
print(f'[IBM Quantum hardware not reachable in this session: {exc}]')
print('Save your IBM Quantum API token first (see Section 1.6), then re-run.')</code></pre><p><strong>Expected Output:</strong></p><div class="box box-generic"><p>▶ Exp 15 — Expected Output &amp; Console Results</p><p>[IBM Quantum hardware not reachable in this session: Unable to find</p><p>account. Please make sure an account with the channel name</p><p>'ibm_quantum_platform' is saved.]</p><p>Save your IBM Quantum API token first (see Section 1.6), then re-run.</p><p>NOTE: with a saved account, this prints e.g.:</p><p>Selected backend: ibm_brisbane</p><p>Job ID: d2n1r5j6k9qc73f8h1i0</p><p>Hardware counts: {'00': 2011, '11': 1975, '01': 58, '10': 52}</p></div><h3 id="full-program-complete-version-13">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ------------------------------------------------------------
# Experiment 15: IBM Quantum Hardware — Complete Circuit Execution
# Dr. S. K. Jain, India
# ------------------------------------------------------------
from qiskit import QuantumCircuit, transpile
from qiskit_aer import AerSimulator
from qiskit_ibm_runtime import QiskitRuntimeService, SamplerV2 as Sampler
import numpy as np, matplotlib.pyplot as plt
qc = QuantumCircuit(2, 2)
qc.h(0); qc.cx(0, 1)
qc.measure([0, 1], [0, 1])
# ---- Part 6: ideal simulation baseline (always available) --------------------
sim = AerSimulator()
sim_counts = sim.run(transpile(qc, sim), shots=4096).result().get_counts()
# ---- Parts 1-5: authenticate, calibrate, transpile, and submit to hardware ---
hw_counts = t1s = t2s = best_error = tqc = backend = None
try:
service = QiskitRuntimeService()
backend = service.least_busy(operational=True, simulator=False)
print(f'Selected backend: {backend.name} ({backend.num_qubits} qubits)')
props = backend.properties()
t1s = [props.t1(q) * 1e6 for q in range(backend.num_qubits)] # microseconds
t2s = [props.t2(q) * 1e6 for q in range(backend.num_qubits)]
readout_errors = [props.readout_error(q) for q in range(backend.num_qubits)]
print(f'Average T1 = {np.mean(t1s):.1f} us, Average T2 = {np.mean(t2s):.1f} us')
print(f'Average readout error = {np.mean(readout_errors):.4f}')
best_pair, best_error = None, 1.0
for gate in props.gates:
if gate.gate in ('cx', 'ecr') and len(gate.qubits) == 2:
err = next((p.value for p in gate.parameters if p.name == 'gate_error'), 1.0)
if err &lt; best_error:
best_error, best_pair = err, tuple(gate.qubits)
print(f'Best two-qubit gate pair: {best_pair} (error = {best_error:.5f})')
tqc = transpile(qc, backend, optimization_level=3,
initial_layout=list(best_pair) if best_pair else None)
print(f'Transpiled circuit depth = {tqc.depth()}, gate count = {tqc.size()}')
sampler = Sampler(backend)
job = sampler.run([tqc], shots=4096)
print(f'IBM Quantum Job ID: {job.job_id()}')
result = job.result()
hw_counts = result[0].data.c.get_counts()
expected_states = {'00', '11'} # ideal Bell state gives only 00 or 11
unexpected_shots = sum(v for k, v in hw_counts.items() if k not in expected_states)
error_rate = unexpected_shots / 4096
print(f'\nHardware error rate (unexpected outcomes) = {error_rate*100:.2f}%')
print(f'Estimated from CX error x depth: '
f'~{best_error * tqc.count_ops().get("cx", 1) * 100:.2f}%')
except Exception as exc:
print(f'\n[IBM Quantum hardware not reachable in this session: {exc}]')
print('Save your IBM Quantum API token first (see Section 1.6), then re-run.')
print('Proceeding with the ideal-simulation panel only.')
# ---- Visualisation --------------------------------------------------------------
fig, axes = plt.subplots(1, 2, figsize=(12, 5))
if hw_counts is not None:
all_keys = sorted(set(hw_counts) | set(sim_counts))
axes[0].bar(np.arange(len(all_keys)) - 0.2, [sim_counts.get(k, 0) for k in all_keys],
width=0.4, label='Simulation (ideal)', color='#55A868')
axes[0].bar(np.arange(len(all_keys)) + 0.2, [hw_counts.get(k, 0) for k in all_keys],
width=0.4, label='Hardware', color='#C44E52')
axes[0].set_xticks(range(len(all_keys))); axes[0].set_xticklabels(all_keys)
axes[0].set_title('Bell State: Simulation vs IBM Hardware')
axes[1].bar(['T1 (us)', 'T2 (us)'], [np.mean(t1s), np.mean(t2s)], color='#4C72B0')
axes[1].set_title(f'Calibration Summary -- {backend.name}')
else:
keys = sorted(sim_counts)
axes[0].bar(keys, [sim_counts[k] for k in keys], color='#55A868', label='Simulation (ideal)')
axes[0].set_title('Bell State: Ideal Simulation Only (no hardware session)')
axes[1].axis('off')
axes[1].text(0.5, 0.5, 'Calibration data unavailable\n(no IBM Quantum session)',
ha='center', va='center')
axes[0].legend(fontsize=8)
plt.tight_layout()
plt.savefig('lab15_ibm_hardware_full_analysis.png', dpi=150)
plt.show()
print('\nNoise sources discussion: T1 relaxation and T2 dephasing accumulate with')
print('circuit depth; two-qubit gate error dominates for entangling circuits;')
print('readout error adds a final, state-independent misclassification chance.')</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image67.png"/></figure><div class="box box-generic"><p>▶ Exp 15 — Expected Output &amp; Console Results</p><p>[IBM Quantum hardware not reachable in this session: Unable to find</p><p>account. Please make sure an account with the channel name</p><p>'ibm_quantum_platform' is saved.]</p><p>Save your IBM Quantum API token first (see Section 1.6), then re-run.</p><p>Proceeding with the ideal-simulation panel only.</p><p>Noise sources discussion: T1 relaxation and T2 dephasing accumulate with</p><p>circuit depth; two-qubit gate error dominates for entangling circuits;</p><p>readout error adds a final, state-independent misclassification chance.</p><p>NOTE: with a saved IBM Quantum account, Parts 1-5 also print the live</p><p>backend name, calibration T1/T2 and readout error, the best qubit pair,</p><p>the Job ID, and a measured hardware error rate (typically 2-8%).</p></div><h2 id="observation-and-results-14">3. Observation and Results</h2><h3 id="observation-tables-7">Observation Tables</h3><p><em>Complete the observation tables for Experiment 15. Fill in during the practical session using actual experimental data from AQLL and your own program.</em></p><table>
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
</table><h3 id="ibm-hardware-execution-record-2">IBM Hardware Execution Record</h3><table>
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
</table><h2 id="discussion-questions-14">4. Discussion Questions</h2><ul>
<li><p>Why does selecting the qubit pair with the lowest two-qubit gate error matter for getting results close to the ideal simulation?</p></li>
<li><p>Explain how T1 relaxation, T2 dephasing, gate errors, and readout errors each contribute differently to the overall error rate you observe.</p></li>
<li><p>If you increased the shot count from 4096 to 40960, would you expect the measured error rate to decrease? Why or why not?</p></li>
<li><p>Write complete answers in your lab record with supporting calculations and diagrams.</p></li>
<li><p>For Phase 2: document your design choices and compare results with theoretical predictions.</p></li>
</ul><h2 id="lab-record-requirements-14">5. Lab Record Requirements</h2><ul>
<li><p>Run your own Qiskit program for Experiment 15. Document all design choices.</p></li>
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
<td><strong>What are the key quantum concepts demonstrated in Experiment 15?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Experiment 15 demonstrates: Capstone experiment: full IBM Quantum workflow. Steps: (1) Authenticate with QiskitRuntimeService, (2) Select backend with service.least_busy(), (3) Read calibration: T₁, T₂, gate errors, readout errors via backend.properties(), (4) Identify best qubit pair by minimum CX error, (5) Transpile with op... Students should understand both the theoretical foundations and the practical Qiskit implementation, and be able to explain the significance of each result in terms of quantum information science principles covered in this manual.</td>
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
<td><strong>Why does the transpiler's optimisation_level parameter affect the final error rate observed on hardware?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Higher optimisation levels apply more aggressive gate-count reduction and noise-adaptive qubit routing/mapping, reducing the number of physical gates (especially error-prone two-qubit gates) needed to implement the circuit, which directly lowers the accumulated probability of an error occurring during execution.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>What is the practical difference between a coherent gate error and a stochastic readout error, and how would each show up in your results?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>A coherent gate error systematically rotates the state in a consistent, predictable direction on every run, which can partially cancel or compound depending on circuit structure. A stochastic readout error instead randomly flips the classical measurement outcome with some fixed probability after the quantum state has already collapsed, appearing as unbiased noise added independently to each measurement.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>Why is it good practice to compare your hardware results against an ideal noiseless simulation of the same circuit, rather than just reporting the hardware counts alone?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Without a noiseless baseline, there is no way to distinguish which features of the hardware results are genuine physics versus artifacts of hardware noise. Comparing against simulation isolates the noise contribution, allowing meaningful estimation of error rates and identification of which noise sources (gate, decoherence, readout) dominate.</td>
</tr>
</tbody>
</table>