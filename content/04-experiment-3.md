<h1 id="experiment-3-entanglement-quantification-and-schmidt-decomposition">Experiment 3: Entanglement Quantification and Schmidt Decomposition</h1><h2 id="background-theory-2">1. Background Theory</h2><p>Entanglement can be quantified by three complementary measures: (1) Single-qubit entanglement entropy <img loading="lazy" src="content/images/image16.png"/>, measuring each qubit's entanglement with the rest. (2) Pairwise concurrence C(i,j) ∈ [0,1] measuring two-qubit entanglement. (3) Schmidt decomposition via SVD, providing the most compact bipartite representation.</p><div class="box box-generic"><p>For GHZ+i:</p><p>S(Qᵢ) = 1 ebit for ALL i (maximal single-qubit entanglement)</p><p>C(Qᵢ,Qⱼ) = 0 for all pairs (GHZ entanglement is genuinely multi-partite, not pairwise!)</p><p>Schmidt rank = 2 for any bipartition, Schmidt entropy = 1 ebit</p><p>Schmidt decomposition (Q0Q1|Q2Q3):</p><p>|GHZ+i⟩ = (1/√2)|00⟩_{Q0Q1}⊗|00⟩_{Q2Q3} + (i/√2)|11⟩_{Q0Q1}⊗|11⟩_{Q2Q3}</p></div><table>
<colgroup>
<col style="width: 23%"/>
<col style="width: 25%"/>
<col style="width: 20%"/>
<col style="width: 31%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Measure</strong></td>
<td><strong>Formula</strong></td>
<td><strong>Value for GHZ+i</strong></td>
<td><strong>Physical Meaning</strong></td>
</tr>
<tr class="even">
<td>Single-qubit entropy</td>
<td>S(Qᵢ)=−Tr(ρᵢ log₂ρᵢ)</td>
<td>1.0 ebit (all qubits)</td>
<td>Maximal entanglement with the rest</td>
</tr>
<tr class="odd">
<td>Concurrence</td>
<td>C(i,j)∈[0,1]</td>
<td>0.000 (all pairs)</td>
<td>GHZ entanglement is genuine multi-qubit, not pairwise</td>
</tr>
<tr class="even">
<td>Schmidt rank</td>
<td># non-zero SVD values</td>
<td>2 (for any bipartition)</td>
<td>Entangled: rank&gt;1 ⇔ entanglement</td>
</tr>
<tr class="odd">
<td>Schmidt entropy</td>
<td>−Σ λᵢ² log₂λᵢ²</td>
<td>1.0 ebit</td>
<td>λ₁=λ₂=1/√2; equal Schmidt coefficients</td>
</tr>
</tbody>
</table><h2 id="qiskit-code-1">2. Qiskit Code</h2><h3 id="first-program-simple-version-2">First Program (Simple Version)</h3><p>This concise program provides the essential code. Run this first to verify the core logic.</p><pre><code class="language-python"># ---------------------------------------------------------------
# Experiment 3 — First Program: Entanglement Quantification (simple version)
# Dr. S. K. Jain, India
# ---------------------------------------------------------------
from qiskit.quantum_info import Statevector, partial_trace, entropy, concurrence
from qiskit import QuantumCircuit
import numpy as np
qc = QuantumCircuit(4)
qc.h(0); qc.p(np.pi/2, 0)
qc.cx(0,1); qc.cx(0,2); qc.cx(0,3)
sv = Statevector(qc)
# Single-qubit entanglement entropy
for qi in range(4):
other = [q for q in range(4) if q != qi]
rho_i = partial_trace(sv, other)
S = float(entropy(rho_i, base=2))
print(f'S(Q{qi}) = {S:.8f} ebits (expected: 1.00000000)')
# Pairwise concurrence
pairs = [(0,1),(0,2),(0,3),(1,2),(1,3),(2,3)]
for (i,j) in pairs:
other = [q for q in range(4) if q not in [i,j]]
rho_ij = partial_trace(sv, other)
C = float(concurrence(rho_ij))
print(f'C(Q{i},Q{j}) = {C:.8f}')
# Schmidt decomposition Q0Q1 | Q2Q3
mat = sv.data.reshape(4, 4)
_, svals, _ = np.linalg.svd(mat)
rank = np.sum(svals &gt; 1e-10)
H = -np.sum((svals[:rank]**2)*np.log2(svals[:rank]**2))
print(f'Schmidt rank = {rank}, entropy = {H:.8f} ebits')</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image17.png"/></figure><h3 id="full-program-complete-version-1">Full Program (Complete Version)</h3><p>This comprehensive program includes step-by-step analysis, multi-panel visualisations, and full output generation.</p><pre><code class="language-python"># ───────────────────────────────────────────────────────────────────────
# Experiment 3: Entanglement Quantification and Schmidt Decomposition
# AQLL §3 | Dr. S. K. Jain, India
# ───────────────────────────────────────────────────────────────────────
from qiskit.quantum_info import Statevector, partial_trace, entropy, concurrence
from qiskit import QuantumCircuit
import numpy as np, matplotlib.pyplot as plt
# Build GHZ+i
qc = QuantumCircuit(4)
qc.h(0); qc.p(np.pi/2, 0)
qc.cx(0,1); qc.cx(0,2); qc.cx(0,3)
sv = Statevector(qc)
# Single-qubit entanglement entropy for all 4 qubits
print('Single-Qubit Entanglement Entropy:')
entropies = []
for qi in range(4):
other_qubits = [q for q in range(4) if q != qi]
rho_i = partial_trace(sv, other_qubits)
S_i = float(entropy(rho_i, base=2))
entropies.append(S_i)
print(f' S(Q{qi}) = {S_i:.8f} ebits (expected: 1.00000000)')
# Pairwise concurrence for all 6 qubit pairs
print('\nPairwise Concurrence C(i,j):')
pairs = [(0,1),(0,2),(0,3),(1,2),(1,3),(2,3)]
concs = []
for (i,j) in pairs:
other = [q for q in range(4) if q not in [i,j]]
rho_ij = partial_trace(sv, other)
C = float(concurrence(rho_ij))
concs.append(C)
print(f' C(Q{i},Q{j}) = {C:.8f} (expected: 0.0 for GHZ state)')
# Schmidt decomposition across Q0Q1 | Q2Q3 bipartition
print('\nSchmidt Decomposition (Q0Q1 | Q2Q3):')
amp_matrix = sv.data.reshape(4, 4)
U, singular_vals, Vh = np.linalg.svd(amp_matrix)
probs = singular_vals**2
probs_nonzero = probs[probs &gt; 1e-12]
schmidt_rank = len(probs_nonzero)
H_schmidt = -np.sum(probs_nonzero * np.log2(probs_nonzero))
print(f' Schmidt rank = {schmidt_rank} (expected: 2)')
print(f' Schmidt coefficients: {singular_vals[:schmidt_rank].round(6)}')
print(f' Schmidt entropy H = {H_schmidt:.8f} ebits (expected: 1.00000000)')
# Visualisation: bar charts
fig, axes = plt.subplots(1, 3, figsize=(17, 5))
# Entropy bar chart
axes[0].bar([f'Q{i}' for i in range(4)], entropies,
color=['#1A3C6E','#2E75B6','#0D5C63','#C09010'], edgecolor='white', linewidth=1.5)
axes[0].axhline(1.0, color='gold', linestyle='--', lw=2, label='Max (1 ebit)')
axes[0].set_ylabel('Entanglement Entropy (ebits)')
axes[0].set_title('Single-Qubit Entanglement Entropy', fontweight='bold')
axes[0].legend(); axes[0].set_ylim(0, 1.3)
# Concurrence bar chart
axes[1].bar([f'Q{i}Q{j}' for i,j in pairs], concs,
color='teal', edgecolor='white', linewidth=1.5)
axes[1].set_ylabel('Concurrence C(i,j)', fontsize=11)
axes[1].set_title('Pairwise Concurrence (GHZ+i state)', fontweight='bold')
axes[1].set_ylim(0, 1.1)
axes[1].tick_params(axis='x', rotation=30)
# Schmidt coefficients
axes[2].bar(range(len(singular_vals)), singular_vals**2,
color='#8B0000', edgecolor='white', linewidth=1.5)
axes[2].set_xlabel('Schmidt index k')
axes[2].set_ylabel('λᵏ² (probability weight)')
axes[2].set_title(f'Schmidt Coefficients²\n(Rank={schmidt_rank}, Entropy ={H_schmidt:.4f})', fontweight='bold')
plt.suptitle('GHZ+i Entanglement Analysis', fontsize=14, fontweight='bold')
plt.tight_layout()
plt.savefig('lab3_entanglement.png', dpi=150, bbox_inches='tight')
plt.show()</code></pre><p><strong>Expected Output:</strong></p><figure class="book-figure"><img loading="lazy" src="content/images/image18.png"/></figure><figure class="book-figure"><img loading="lazy" src="content/images/image19.png"/></figure><pre><code>▶ Exp 3 — Expected Output &amp; Console Results
CONSOLE OUTPUT:
Single-Qubit Entanglement Entropy:
S(Q0) = 1.00000000 ebits (expected: 1.00000000)
S(Q1) = 1.00000000 ebits
S(Q2) = 1.00000000 ebits
S(Q3) = 1.00000000 ebits
Pairwise Concurrence C(i,j):
C(Q0,Q1) = 0.00000000 (GHZ entanglement is NOT pairwise!)
C(Q0,Q2) = 0.00000000
C(Q0,Q3) = 0.00000000
C(Q1,Q2) = 0.00000000
C(Q1,Q3) = 0.00000000
C(Q2,Q3) = 0.00000000
Schmidt Decomposition (Q0Q1 | Q2Q3):
Schmidt rank = 2 (expected: 2)
Schmidt coefficients: [0.707107 0.707107]
Schmidt entropy H = 1.00000000 ebits (expected: 1.00000000)
INTERPRETATION: Each qubit has S=1 ebit (maximal entanglement with the rest)
but pairwise concurrence=0 (GHZ entanglement is genuine 4-partite, not 2-body).</code></pre><h2 id="observation-and-results-2">3. Observation and Results</h2><h3 id="table-3.1-single-qubit-entanglement-entropy">Table 3.1 — Single-Qubit Entanglement Entropy</h3><table>
<colgroup>
<col style="width: 16%"/>
<col style="width: 21%"/>
<col style="width: 21%"/>
<col style="width: 21%"/>
<col style="width: 19%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Qubit</strong></td>
<td><strong>S(Qᵢ) from AQLL (ebits)</strong></td>
<td><strong>S(Qᵢ) Own Code (ebits)</strong></td>
<td><strong>Expected (ebits)</strong></td>
<td><strong>Maximal?</strong></td>
</tr>
<tr class="even">
<td>Q0</td>
<td></td>
<td></td>
<td>1.00000000</td>
<td>Yes/No</td>
</tr>
<tr class="odd">
<td>Q1</td>
<td></td>
<td></td>
<td>1.00000000</td>
<td>Yes/No</td>
</tr>
<tr class="even">
<td>Q2</td>
<td></td>
<td></td>
<td>1.00000000</td>
<td>Yes/No</td>
</tr>
<tr class="odd">
<td>Q3</td>
<td></td>
<td></td>
<td>1.00000000</td>
<td>Yes/No</td>
</tr>
</tbody>
</table><h3 id="table-3.2-pairwise-concurrence">Table 3.2 — Pairwise Concurrence</h3><table>
<colgroup>
<col style="width: 25%"/>
<col style="width: 25%"/>
<col style="width: 25%"/>
<col style="width: 25%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Qubit Pair</strong></td>
<td><strong>C(i,j) from AQLL</strong></td>
<td><strong>C(i,j) Own Code</strong></td>
<td><strong>Comment</strong></td>
</tr>
<tr class="even">
<td>C(Q0,Q1)</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>C(Q0,Q2)</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>C(Q0,Q3)</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>C(Q1,Q2)</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>C(Q1,Q3)</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>C(Q2,Q3)</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><h3 id="table-3.3-schmidt-decomposition-q0q1-q2q3">Table 3.3 — Schmidt Decomposition (Q0Q1 | Q2Q3)</h3><table>
<colgroup>
<col style="width: 25%"/>
<col style="width: 25%"/>
<col style="width: 25%"/>
<col style="width: 25%"/>
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Schmidt Index k</strong></td>
<td><strong>Coefficient λᵏ</strong></td>
<td><strong>λᵏ² (probability)</strong></td>
<td><strong>Schmidt Entropy H</strong></td>
</tr>
<tr class="even">
<td>k=0</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="odd">
<td>k=1</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>k=2 onwards</td>
<td>~0</td>
<td>~0</td>
<td></td>
</tr>
<tr class="odd">
<td>Schmidt Rank →</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td>Schmidt Entropy H →</td>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><h2 id="discussion-questions-2">4. Discussion Questions</h2><ol type="1">
<li><p>The pairwise concurrences C(Qᵢ,Qⱼ) for GHZ+i are all zero, yet all single-qubit entropies S(Qᵢ) are 1 ebit. How do you reconcile these apparently contradictory results?</p></li>
<li><p>Compare the entanglement structure of GHZ+i with a product of Bell pairs: (|Φ+⟩_{Q0Q1})⊗(|Φ+⟩_{Q2Q3}). Which has higher pairwise concurrence? Which has higher Schmidt entropy?</p></li>
<li><p>The Schmidt rank of GHZ+i across Q0Q1|Q2Q3 is 2. What is the Schmidt rank across Q0|Q1Q2Q3? Compute analytically.</p></li>
<li><p>If you measured Q0 in the Z basis and found |0⟩, what state do Q1,Q2,Q3 collapse to? What are their new entanglement entropies?</p></li>
</ol><h2 id="lab-record-requirements-2">5. Lab Record Requirements</h2><ul>
<li><p>Run AQLL §3. Screenshot both output figures (S3_entanglement.png and S3_schmidt.png).</p></li>
<li><p>Run the First Program. Record all entropy and concurrence values in the tables.</p></li>
<li><p>Run the Full Program. Verify entropy and Schmidt decomposition results.</p></li>
<li><p>Modify the state to a product state |+⟩⁴ and re-run. Record concurrence and entropy values.</p></li>
<li><p>Write answers to all Discussion Questions with supporting calculations.</p></li>
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
<td><strong>What is entanglement entropy and what does 1 ebit mean?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Entanglement entropy S(ρ_A) = −Tr(ρ_A log₂ρ_A) measures the entanglement of subsystem A with the rest. 1 ebit is the maximum, achieved when S=1, corresponding to ρ_A = I/2 (maximally mixed single-qubit state). For GHZ+i, each qubit has 1 ebit of entanglement with the remaining three qubits. The ebit is the fundamental unit of quantum entanglement, analogous to the bit as the unit of classical information.</td>
</tr>
<tr class="even">
<td><strong>Q2</strong></td>
<td><strong>Why is pairwise concurrence C(Qᵢ,Qⱼ) = 0 for GHZ+i even though the state is entangled?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>GHZ entanglement is genuinely multi-partite — it cannot be decomposed into pairwise Bell-pair entanglement. The reduced 2-qubit states ρᵢⱼ = Tr_{rest}(ρ_GHZ) are classical mixtures (diagonal mixed states) with zero concurrence. This is the entanglement paradox: the whole is maximally entangled but no two-body pair is entangled. Compare with W state: (|100⟩+|010⟩+|001⟩)/√3 which DOES have non-zero pairwise concurrence (C=2/3).</td>
</tr>
<tr class="even">
<td><strong>Q3</strong></td>
<td><strong>What is the Schmidt decomposition and why is it useful?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The Schmidt decomposition is a canonical bipartite representation: |ψ_AB⟩ = Σᵏ λᵏ |aᵏ⟩_A |bᵏ⟩_B where {λᵏ} are Schmidt coefficients obtained via SVD of the coefficient matrix. Useful because: (1) Schmidt rank determines entanglement (rank 1 = product state), (2) Schmidt coefficients determine entanglement entropy, (3) provides the most efficient bipartite representation. For GHZ+i, Schmidt rank=2 and both coefficients are 1/√2, confirming maximal entanglement.</td>
</tr>
<tr class="even">
<td><strong>Q4</strong></td>
<td><strong>How is the Schmidt decomposition related to Singular Value Decomposition (SVD)?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>For an n-qubit state |ψ⟩ with coefficient tensor, reshape it into a matrix M across some bipartition. Apply SVD: M = UΣV†. The diagonal entries σᵏ of Σ are the Schmidt coefficients λᵏ = σᵏ, the columns of U are the |aᵏ⟩ states, and the rows of V† are the |bᵏ⟩ states. The Schmidt rank equals the number of non-zero singular values.</td>
</tr>
<tr class="even">
<td><strong>Q5</strong></td>
<td><strong>What is the difference between separable and entangled states?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>A separable (product) state: |ψ_AB⟩ = |ψ_A⟩⊗|ψ_B⟩, Schmidt rank=1. Entangled states cannot be written as any product — measurements on A and B are correlated beyond classical means. For mixed states: ρ_AB is separable if ρ_AB = Σᵢ pᵢ ρᵢ_A⊗ρᵢ_B. Entanglement cannot be created by local operations and classical communication (LOCC), making it a genuine quantum resource.</td>
</tr>
<tr class="even">
<td><strong>Q6</strong></td>
<td><strong>What is the monogamy of entanglement?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Monogamy states that if two qubits A and B are maximally entangled (share 1 ebit), neither can be entangled with any third party C. For GHZ+i: S(Q0)=1 ebit but this entanglement is shared among all four qubits collectively. The pairwise concurrence C(Q0,Q1)=0 confirms that Q0 cannot be maximally entangled with Q1 alone — it shares entanglement with Q1,Q2,Q3 jointly. This is why quantum key distribution is secure: a third party cannot share entanglement without disturbing the original pair.</td>
</tr>
<tr class="even">
<td><strong>Q7</strong></td>
<td><strong>What is quantum discord and how does it differ from entanglement?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Quantum discord Q(A:B) = I(A:B) − J(A:B) captures quantum correlations beyond entanglement. Even separable states can have non-zero discord. For GHZ+i: entanglement is maximal but pairwise concurrence=0. Discord measures the minimal disturbance caused by any local measurement. While GHZ pairwise concurrence=0, the pairwise discord is non-zero — there are quantum correlations that cannot be explained by a product state even between just two qubits of the GHZ state.</td>
</tr>
<tr class="even">
<td><strong>Q8</strong></td>
<td><strong>Explain the concept of LOCC and its relevance to entanglement.</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>LOCC (Local Operations and Classical Communication): operations where spatially separated parties each perform quantum operations on their local qubits and communicate classically. Entanglement cannot be created under LOCC — it is a quantum resource. Entanglement measures must be non-increasing under LOCC (monotonicity). Entanglement entropy satisfies this. Converting between GHZ and W states via LOCC is impossible with certainty, showing they represent distinct classes of multi-partite entanglement.</td>
</tr>
<tr class="even">
<td><strong>Q9</strong></td>
<td><strong>What are Bell states and how do they relate to GHZ states?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Bell states: |Φ±⟩=(|00⟩±|11⟩)/√2 and |Ψ±⟩=(|01⟩±|10⟩)/√2 — maximally entangled 2-qubit basis. Schmidt rank=2, concurrence=1 for all Bell states. GHZ state: (|000⟩+|111⟩)/√2 is the 3+ qubit generalisation. Key difference: Bell states have concurrence=1 (pairwise maximal), GHZ states have concurrence=0 but multi-partite entanglement. Bell states are resources for teleportation; GHZ states are resources for secret sharing.</td>
</tr>
<tr class="even">
<td><strong>Q10</strong></td>
<td><strong>How does partial trace work mathematically?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>For bipartite system AB with density matrix ρ_AB = Σ_{ijkl} c_{ijkl} |i⟩_A⟨j|_A ⊗ |k⟩_B⟨l|_B, the partial trace over B is: ρ_A = Tr_B(ρ_AB) = Σ_k ⟨k|_B ρ_AB |k⟩_B = Σ_{ijk} c_{ijkk} |i⟩_A⟨j|_A. Physically: we sum over (average over) all possible states of B — equivalent to discarding B. The result ρ_A is a mixed state whenever the original ρ_AB was entangled.</td>
</tr>
<tr class="even">
<td><strong>Q11</strong></td>
<td><strong>What does concurrence measure and how is it calculated?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Concurrence C ∈ [0,1] measures entanglement for a 2-qubit density matrix. C=0 for separable states, C=1 for Bell states. Calculation: C = max(0, λ₁−λ₂−λ₃−λ₄) where λᵢ are square roots of eigenvalues of ρ·(σ_y⊗σ_y)ρ*(σ_y⊗σ_y) in decreasing order. Entanglement of formation EoF = h((1+√(1−C²))/2) where h is binary entropy. For GHZ+i reduced 2-qubit states: C=0 because the reduced states are diagonal mixed states (no off-diagonal coherence).</td>
</tr>
<tr class="even">
<td><strong>Q12</strong></td>
<td><strong>Compare entanglement in GHZ states vs W states.</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>GHZ: (|000⟩+|111⟩)/√2 — fully correlated; measuring one qubit determines all others; single-qubit entropy S=1 ebit; pairwise concurrence=0. W state: (|100⟩+|010⟩+|001⟩)/√3 — pairwise concurrence C=2/3 ≠ 0; more robust under qubit loss; single-qubit entropy S=log₂(3/2)≈0.585 ebit. They are inequivalent under LOCC: no local operations can convert GHZ→W or W→GHZ with certainty, placing them in fundamentally different entanglement classes.</td>
</tr>
<tr class="even">
<td><strong>Q13</strong></td>
<td><strong>What is the maximum possible Schmidt rank for a 4-qubit state across Q0Q1|Q2Q3?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>The Q0Q1|Q2Q3 bipartition: left space has dimension 2²=4, right space 2²=4. The coefficient matrix M is 4×4. Maximum Schmidt rank = min(4,4) = 4. For GHZ+i, Schmidt rank=2 (not maximal). A state with Schmidt rank 4 would have entropy S=log₂(4)=2 ebits — more entangled. Such states exist (e.g. 2-ebit states used in quantum communication protocols) but require different circuit constructions.</td>
</tr>
<tr class="even">
<td><strong>Q14</strong></td>
<td><strong>What is the entanglement entropy of the half of a maximally entangled n-qubit state?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>For an n-qubit state that is maximally entangled across a bipartition of n/2 qubits each, the entanglement entropy equals (n/2) ebits — the maximum possible for that bipartition. For GHZ+i across any bipartition: entropy = 1 ebit regardless of how many qubits are on each side. This is because GHZ is a superposition of only TWO product states, giving Schmidt rank=2 and entropy=log₂(2)=1 ebit for ALL bipartitions.</td>
</tr>
<tr class="even">
<td><strong>Q15</strong></td>
<td><strong>How can entanglement be detected experimentally without full state tomography?</strong></td>
</tr>
<tr class="odd">
<td><strong>A</strong></td>
<td>Witness operators: observable W such that Tr(Wρ_sep)≥0 for all separable ρ_sep, but Tr(Wρ_ent)&lt;0 for entangled ρ_ent. Example for GHZ: W=3I/4−|Φ+⟩⟨Φ+|−|Φ-⟩⟨Φ-|. Bell inequality violations (CHSH, GHZ-Mermin) also certify entanglement. AQLL measures ⟨ZZ⟩, ⟨XX⟩ etc. — if |⟨XX⟩|+|⟨YY⟩|&gt;1/2, entanglement is certified. Full state tomography (Exp 16 in Lab II) provides complete characterisation but requires exponentially many measurements.</td>
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
<p><strong>4</strong></p>
<p>3 hrs</p></td>
<td><p><strong>Purity and Fidelity Tracker — Gate-by-Gate Analysis</strong></p>
<p>AQLL §4 | Phase 1: Guided Simulation</p></td>
</tr>
<tr class="even">
<td><strong>AIM</strong></td>
<td>Verify that unitary gates preserve purity exactly (Tr(ρ²) = 1.00000000 at every step) and track the fidelity of the evolving state relative to the ground state |0000⟩.</td>
</tr>
<tr class="odd">
<td><strong>Module</strong></td>
<td>AQLL §4 | Phase 1: Guided Simulation</td>
</tr>
</tbody>
</table>