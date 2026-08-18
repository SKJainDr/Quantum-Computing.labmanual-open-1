<h1 id="experiment-13-quantum-random-number-generator-statistical-testing">Experiment 13: Quantum Random Number Generator — Statistical Testing</h1><h2 id="background-theory-12">1. Background Theory</h2><p>QRNG exploits the Born rule: qubit in <img loading="lazy" src="content/images/image61.png"/> has P(0)=P(1)=0.5 exactly — fundamental physical randomness, not pseudo-random. For 8-qubit QRNG: generates integers 0-255. Quality tests: (1) Chi-squared uniformity test (p &gt; 0.05 = pass), (2) Kolmogorov-Smirnov test, (3) Autocorrelation function (ACF ≈ 0 for all lags &gt; 0), (4) Shannon entropy H = n bits for n-bit uniform generator.</p><h2 id="qiskit-code-11">2. Qiskit Code</h2><h3 id="first-program-simple-version-12">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># Experiment 13 — First Program: Quantum Random Number Generator — Statistical Testing
# Phase 2 | Dr. S. K. Jain, Invertis University, India
# Quantum Random Number Generator — Statistical Testing
# -------------------------------------------------------------------------
from qiskit import QuantumCircuit, transpile
from qiskit_aer import AerSimulator
import numpy as np
n_bits = 8
qc = QuantumCircuit(n_bits, n_bits)
qc.h(range(n_bits))
qc.measure(range(n_bits), range(n_bits))
sim = AerSimulator()
n_samples = 20
result = sim.run(transpile(qc, sim), shots=n_samples, memory=True).result()
bitstrings = result.get_memory()
values = [int(b, 2) for b in bitstrings]
print(f'{n_samples} quantum random 8-bit integers (0-255):')
print(values)
print(f'\nMean = {np.mean(values):.2f} (expected ~127.5)')
print(f'Std = {np.std(values):.2f} (expected ~73.6 for uniform[0,255])')</code></pre><p><strong>Expected Output:</strong></p><div class="box box-generic"><p>▶ Exp 13 — Expected Output &amp; Console Results</p><p>20 quantum random 8-bit integers (0-255):</p><p>[234, 135, 180, 236, 230, 149, 35, 149, 230, 221, 126, 109, 12, 14, 34, 144, 229, 215, 38, 11]</p><p>Mean = 136.55 (expected ~127.5)</p><p>Std = 83.26 (expected ~73.6 for uniform[0,255])</p></div><h3 id="full-program-complete-version-11">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ───────────────────────────────────────────────────────────────────────
# Experiment 13: Quantum Random Number Generator — Statistical Testing
# Phase 2 | Dr. S. K. Jain, Invertis University, India
# ───────────────────────────────────────────────────────────────────────
from qiskit import QuantumCircuit, transpile
from qiskit_aer import AerSimulator
import numpy as np
from scipy import stats
import matplotlib.pyplot as plt
n_bits = 8
N_shots = 4096
qc = QuantumCircuit(n_bits, n_bits)
qc.h(range(n_bits))
qc.measure(range(n_bits), range(n_bits))
sim = AerSimulator()
result = sim.run(transpile(qc, sim), shots=N_shots, memory=True).result()
bitstrings = result.get_memory()
qrng_values = np.array([int(b, 2) for b in bitstrings])
# ---- Part 1: chi-squared uniformity test ------------------------------------
observed, _ = np.histogram(qrng_values, bins=range(257))
expected = np.full(256, N_shots / 256)
chi2, p_value = stats.chisquare(observed, expected)
print(f'Chi-squared statistic = {chi2:.2f}, p-value = {p_value:.4f} '
f'({"PASS" if p_value &gt; 0.05 else "FAIL"} at alpha=0.05)')
# ---- Part 2: Kolmogorov-Smirnov test against uniform[0,255] -----------------
ks_stat, ks_p = stats.kstest(qrng_values, 'uniform', args=(0, 255))
print(f'KS statistic = {ks_stat:.4f}, p-value = {ks_p:.4f} '
f'({"PASS" if ks_p &gt; 0.05 else "FAIL"} at alpha=0.05)')
# ---- Part 3: autocorrelation function ----------------------------------------
def autocorr(x, lag):
x = x - np.mean(x)
return np.sum(x[:-lag] * x[lag:]) / np.sum(x * x) if lag &gt; 0 else 1.0
lags = range(0, 11)
acf = [autocorr(qrng_values.astype(float), lag) for lag in lags]
print('\nAutocorrelation function (should be ~0 for lag &gt; 0):')
for lag, val in zip(lags, acf):
print(f' lag={lag}: ACF={val:+.4f}')
# ---- Part 4: Shannon entropy ---------------------------------------------------
bit_array = np.array([[int(bit) for bit in b] for b in bitstrings])
p1 = np.mean(bit_array, axis=0)
entropy_per_bit = -(p1 * np.log2(p1 + 1e-12) + (1 - p1) * np.log2(1 - p1 + 1e-12))
total_entropy = np.sum(entropy_per_bit)
print(f'\nShannon entropy per output = {total_entropy:.4f} bits '
f'(ideal = {n_bits}.0000 bits for {n_bits}-bit uniform generator)')
# ---- Part 5: comparison with classical PRNG -------------------------------------
classical_values = np.random.randint(0, 256, size=N_shots)
classical_observed, _ = np.histogram(classical_values, bins=range(257))
chi2_c, p_c = stats.chisquare(classical_observed, expected)
print(f'\nClassical PRNG comparison: chi2={chi2_c:.2f}, p={p_c:.4f}')
# ---- Visualisation ----------------------------------------------------------------
fig, axes = plt.subplots(2, 2, figsize=(12, 9))
axes[0, 0].hist(qrng_values, bins=32, color='#4C72B0', alpha=0.8, label='QRNG')
axes[0, 0].axhline(N_shots / 32, color='red', linestyle='--', label='Expected uniform')
axes[0, 0].set_title('QRNG Output Distribution (8-bit values)')
axes[0, 0].legend(fontsize=8)
axes[0, 1].bar(lags, acf, color='#55A868')
axes[0, 1].axhline(0, color='black', linewidth=0.8)
axes[0, 1].set_title('Autocorrelation Function')
axes[0, 1].set_xlabel('Lag')
axes[1, 0].bar(range(n_bits), p1, color='#DD8452')
axes[1, 0].axhline(0.5, color='black', linestyle='--')
axes[1, 0].set_ylim(0, 1)
axes[1, 0].set_title('P(bit=1) for Each of the 8 Qubits')
axes[1, 0].set_xlabel('Bit index')
axes[1, 1].bar(['Chi-sq p', 'KS p'], [p_value, ks_p], color='#C44E52')
axes[1, 1].axhline(0.05, color='black', linestyle='--', label='alpha=0.05')
axes[1, 1].set_title('Statistical Test p-values (QRNG)')
axes[1, 1].legend(fontsize=8)
plt.tight_layout()
plt.savefig('lab13_qrng_full_analysis.png', dpi=150)
plt.show()</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image62.png"/></figure><div class="box box-generic"><p>▶ Exp 13 — Expected Output &amp; Console Results</p><p>Chi-squared statistic = 248.31, p-value = 0.5680 (PASS at alpha=0.05)</p><p>KS statistic = 0.0106, p-value = 0.7439 (PASS at alpha=0.05)</p><p>Autocorrelation function (should be ~0 for lag &gt; 0):</p><p>lag=0: ACF=+1.0000 lag=1: ACF=+0.0179 lag=2: ACF=+0.0141</p><p>lag=3: ACF=-0.0214 lag=4: ACF=+0.0042 lag=5: ACF=+0.0137</p><p>Shannon entropy per output = 7.9988 bits (ideal = 8.0000 bits)</p><p>Classical PRNG comparison: chi2=285.50, p=0.0919</p></div><h2 id="observation-and-results-12">3. Observation and Results</h2><h3 id="observation-tables-5">Observation Tables</h3><p><em>Complete the observation tables for Experiment 13. Fill in during the practical session using actual experimental data from AQLL and your own program.</em></p><table>
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
</table><h3 id="ibm-hardware-execution-record">IBM Hardware Execution Record</h3><table>
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
</table><h2 id="discussion-questions-12">4. Discussion Questions</h2><ul>
<li><p>Why is a quantum random number generator considered to produce “true” randomness, whereas a classical pseudo-random number generator does not?</p></li>
<li><p>If the chi-squared test on your generated bits failed (p &lt; 0.05), what physical or implementation issues might explain this on real hardware?</p></li>
<li><p>Why must the autocorrelation function be close to zero for every nonzero lag, and what would a nonzero ACF indicate about the generator?</p></li>
<li><p>Write complete answers in your lab record with supporting calculations and diagrams.</p></li>
<li><p>For Phase 2: document your design choices and compare results with theoretical predictions.</p></li>
</ul><h2 id="lab-record-requirements-12">5. Lab Record Requirements</h2><ul>
<li><p>Run your own Qiskit program for Experiment 13. Document all design choices.</p></li>
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
<td><strong>What are the key quantum concepts demonstrated in Experiment 13?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Experiment 13 demonstrates: QRNG exploits the Born rule: qubit in |+⟩=(|0⟩+|1⟩)/√2 has P(0)=P(1)=0.5 exactly — fundamental physical randomness, not pseudo-random. For 8-qubit QRNG: generates integers 0-255. Quality tests: (1) Chi-squared uniformity test (p &gt; 0.05 = pass), (2) Kolmogorov-Smirnov test, (3) Autocorrelation functi... Students should understand both the theoretical foundations and the practical Qiskit implementation, and be able to explain the significance of each result in terms of quantum information science principles covered in this manual.</td>
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
<td><strong>Why does an 8-qubit QRNG circuit use independent Hadamard gates on each qubit rather than a single entangling circuit?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Each qubit's Hadamard-generated superposition and subsequent measurement is an independent fair coin flip; entangling the qubits would introduce correlations between the resulting bits, which would violate the requirement that each of the 256 possible 8-bit outputs be equally likely and statistically independent.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>What does it mean if the Shannon entropy computed from your QRNG output is measurably less than the ideal n bits?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>An entropy below the ideal n bits indicates that the generator's bits are not perfectly uniform and independent — some bias or correlation exists, most likely from decoherence, gate imperfections, or measurement (readout) errors introducing a systematic skew rather than perfectly random outcomes.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>Why is a quantum random number generator generally considered more suitable for cryptographic applications than a classical pseudo-random number generator?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>A classical PRNG is fundamentally deterministic — anyone who knows its algorithm and seed can reproduce its entire output sequence, which is a serious cryptographic weakness. A QRNG's randomness instead derives from the fundamentally probabilistic nature of quantum measurement, so its output cannot in principle be predicted even with full knowledge of the device and its state preparation.</td>
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
<p><strong>14</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Bell States and CHSH Inequality — Full IBM Hardware Test</strong></p>
<p>Phase 2 | Own Code | IBM Hardware | Sem III</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Prepare all four Bell states, verify their properties, and test the CHSH inequality on IBM Quantum hardware; compare hardware |S| with ideal 2√2.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>Phase 2 | Own Code | IBM Hardware | Sem III</td>
</tr>
</tbody>
</table>