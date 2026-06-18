# Módulo 3 — Máscara de significancia local (inferencia honesta)

```python
import numpy as np

def t_critico(res, alpha=0.05, ajustado=True):
    """t crítico. Para MGWR usar el ajustado (Da Silva & Fotheringham)."""
    if ajustado and hasattr(res, "critical_tval"):
        tc = res.critical_tval(alpha=alpha)   # corrige por comparaciones múltiples
        # devuelve un valor por variable; tomar el correspondiente o el máximo conservador
        return tc
    return 1.96   # fallback 95% sin corrección

def mascara_significancia(res, idx, alpha=0.05):
    """Vector booleano: True = significativo para la variable idx."""
    t = res.tvalues[:, idx]
    tc = t_critico(res, alpha)
    tc_i = tc[idx] if np.ndim(tc) else tc
    sig = np.abs(t) >= tc_i
    print(f"Variable {idx}: t_crit={tc_i:.3f}  significativas={sig.sum()}/{len(sig)}")
    return sig

def correccion_multiple(pvals, alpha=0.05, metodo="fdr_bh"):
    """
    Pruebas múltiples (Wheeler): con n manzanas corres n pruebas; ~5% saldrán
    'significativas' por azar (clústeres fantasma). Reajustar el p crítico.
    metodo: 'bonferroni' (estricto) o 'fdr_bh' (Benjamini-Hochberg, recomendado).
    Devuelve máscara booleana de significancia corregida.
    """
    from statsmodels.stats.multitest import multipletests
    pvals = np.asarray(pvals)
    ok = ~np.isnan(pvals)
    rechaza = np.zeros(len(pvals), dtype=bool)
    rechaza[ok], p_corr, *_ = multipletests(pvals[ok], alpha=alpha, method=metodo)
    print(f"{metodo}: {rechaza.sum()}/{ok.sum()} significativas tras corregir "
          f"(antes sin corregir: {(pvals[ok] < alpha).sum()})")
    return rechaza

def aplicar_mascara(gdf, valores, sig, col="beta"):
    """Anula color donde NO es significativo: NaN -> se pinta con missing_kwds."""
    gdf = gdf.copy()
    v = np.where(sig, valores, np.nan)
    gdf[col] = v
    return gdf

# Render con zonas no significativas en gris/tramado:
def plot_enmascarado(gdf, col, norm, cmap="RdBu_r", ax=None):
    return gdf.plot(
        column=col, cmap=cmap, norm=norm, ax=ax,
        edgecolor="0.8", linewidth=0.2, legend=True,
        missing_kwds=dict(color="0.85", hatch="///", edgecolor="0.6",
                          label="No significativo (|t|<t_crit)"),
    )
```

Regla dura: si `|t_i| < t_crit`, la unidad se pinta neutra (gris plano, `alpha=0`
o tramado). Nunca discutir resultados sobre zonas no significativas.
