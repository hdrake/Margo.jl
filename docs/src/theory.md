# Theory

## The causal chain of climate suffering

It is helpful to conceptualize climate change as a four step process that connects the human-caused emissions of greenhouse gases (GHGs) to the eventual climate suffering (or damages) that they cause. This four-step framing is helpful because it highlights the four major human interventions (or controls) which can break these links and reduce climate suffering: **M**itigation of GHG emissions (e.g. switching away from fossil fuels), **R**emoval of carbon dioxide from the atmosphere (e.g. by planting trees or storing biofuel emissions underground), **G**eoengineering by solar radiation management (e.g. injecting sulfate aerosols into the stratosphere to reduce incoming sunlight), or **A**dapting to the changed climate (e.g. relocating coastal communities displaced by rising seas or expanding indoor air condititiong to fight off intensifying heat waves).

```math
\text{Emissions}
\xrightarrow{\text{\textbf{Mitigation}}}
\text{GHGs}
\xrightarrow{\text{\textbf{Removal}}}
\text{Forcing}
\xrightarrow{\text{\textbf{Geoengineering}}}
\text{Warming}
\xrightarrow{\text{\textbf{Adaptation}}}
\text{Suffering}.
```

Letting climate change run rampant would cause a lot of damage; however, climate interventions that keep climate change under control are not free either. With MARGO, we allow users to explore the trade-offs between climate interventions and climate suffering by employing an optimization framework commonly used in the climate-economics community. In this section, we develop a simple theory– a set of mathematical equations– which capture the key aspects of the entire causal chain of climate damages.

!!! ukw "Tuning MARGO's free parameters"
    We have attempted to make reasonable choices for the default values of MARGO's parameters in the equations below. Some of these, such as the climate physics variables, are well justified based on the best climate science research; others, such as the socio-economic parameters, are chosen based on our values (or those represented in the academic climate change literature). We encourage users to question these choices and plug their own values into ClimateMARGO. If you feel strongly that we should change some of the default values included in the model code, please reach out to us by [opening an issue](https://github.com/hdrake/ClimateMARGO.jl/labels/criticism) on Github.

## Greenhouse gas emissions

CO``_{2e}`` is emitted at a rate ``q(t)``, with only a fraction ``r = 50\%`` remaining in the atmosphere after a few years, net of uptake by the ocean and terrestrial biosphere.

!!! note "Climate intervention: Emissions mitigation"
    GHG emissions are reduced by a **M**itigation factor ``(1-M(t))``, becoming ``q(t)(1-M(t))``, where ``M(t)`` varies between 0% (high-emissions baseline) and 100% (full decarbonization).

## Greenhouse gas concentrations

CO``_{2e}`` continues to accumulate in the atmosphere and its concentrations ``c(t)= c_{0} + \int_{t_{0}}^{t} rq(t)\text{ d}t\,`` will increase as long as the emissions ``q(t)`` are greater than zero.

!!! note "Climate intervention: Carbon dioxide removal"
    **R**emoval of CO``_{2e}`` in contrast to mitigation, is de-coupled from instantaneous emissions and is expressed as the percentage of 2020 baseline emissions that are removed from the atmosphere in a given year, ``q_{0}R(t)``. A maximal value of ``R=100\%`` corresponds to removing ``30`` GtCO``_{2e}`` per year, which is roughly equal to a recent upper-bound estimate of the global potential for negative emission technologies.

    A useful diagnostic quantity is the effective emissions
    ```math
        rq(t)(1-M(t)) - rq_{0}R(t),
    ```
    which is the annual rate of CO``_{2e}`` accumulation in the atmosphere, with contributions from both emissions mitigation and CDR.

    The change in CO``_{2e}`` concentrations is simply the integral of the effective emissions over time,
    ```math
        c_{M,R}(t) = c_{0} + \int_{t_{0}}^{t} rq(t')(1-M(t')) \text{ d}t' - rq_{0} \int_{t_{0}}^{t} R(t')\text{ d}t'.
    ```

## Radiative forcing from the greenhouse effect

Increasing CO``_{2e}`` concentrations strengthen the greenhouse effect, reducing outgoing longwave radiation and causing an increased radiative forcing of ``F(t) = a \ln(c(t)/c_{0})``, which exerts a warming effect on Earth's surface.

!!! note "Climate intervention: Solar geo-engineering"
    **G**eoengineering by solar radiation management acts to offset a fraction of the CO``_{2e}`` forcing,
    ```math
        F_{M,R,G}(t) = F_{M,R}(t) - G(t) F_{\infty},
    ```
    where ``F_{M,R} = a \ln(c_{M,R}(t) / c_{0})`` is the controlled CO``_{2e}`` forcing (i.e. accounting for any mitigation and carbon dioxide removal) and ``F_{\infty} = 8.5`` W/m``^{2}`` is the maximum baseline CO``_{2e}`` forcing, which is attained starting in 2150 when baseline emissions are assumed to reach zero. A value of ``G = 100\%`` thus corresponds to a complete cancellation between the equilibrium warming from baseline CO``_{2e}`` increases and the cooling from a full deployment of SRM.

## Global warming and climate suffering

Near-surface air temperatures eventually increase by ``T(t) = F(t)/B`` to balance the reduced cooling to space, where ``B/(\kappa + B) = 60\%`` of the warming occurs within a few years and the remaining ``\kappa/(B + \kappa) = 40\%`` occurs over the course of several centuries due to ocean heat uptake. The feedback parameter ``B`` includes the effects of all climate feedbacks, except those involving the carbon cycle and the long-term ice sheet response, and the ocean heat uptake rate ``\kappa`` parameterizes the combined effects of advection and diffusion of heat into the deep ocean.

More specifically, the controlled near-surface air temperature evolves according to the total controlled forcing,
```math
    T_{M,R,G}(t) - T_{0} = \frac{F_{M,R,G}(t)}{B + \kappa} + \frac{\kappa}{B} \int_{t_{0}}^{t} \frac{e^{\frac{t'-t}{\tau_{D}}}}{\tau_{D}} \frac{F_{M,R,G}(t')}{B+\kappa} \, \text{d}t',
```
where ``T_{0} = 1.1``°C is the present warming relative to preindustrial and ``\tau_{D} = 240`` years is the slow timescale of ocean heat uptake. The first term on the right-hand side represents a fast transient response while the second term represents a slow recalcitrant response due to the thermal inertia of the deep ocean. Climate inertia decouples the temperature response from instantaneous forcing and implies that an additional fraction of short-term warming (or cooling) is locked in for the future, even if radiative forcing is stabilized, as in the case of bringing emissions to zero in our model.

Anthropogenic warming causes a myriad of climate impacts, which result suffering (expressed in an economic model as a loss in welfare or monetary damages) that increase non-linearly with temperature, ``D = \beta T^{2}``, where the damage parameter ``\beta`` is tuned such that a warming of ``3``°C results in damages of the ``2\%`` of Gross World Product (GWP), consistent with DICE in the limit of non-catastrophic warming.

!!! note "Climate intervention: Adaptation"
    **A**daptation to climate impacts acts to reduce damages by a fraction ``A(t)``. Since some climate impacts are likely impossible to adapt to, we assume that adaptation can at most reduce climate damages by ``A < 40\%``. The controlled damages are thus given by
    ```math
        D_{M,R,G,A} = \beta (T_{M,R,G})^{2} (1-A(t)).
    ```
    Although adaptation does not affect the planetary temperature directly, it is useful to consider an "adapted temperature" ``T_{M,R,G,A}`` which yields controlled damages equivalent to the fully-controlled damages ``\beta (T_{M,R,G,A})^{2} = \beta (T_{M,R,G})^{2} (1-A)`` and is defined
    ```math
        T_{M,R,G,A} \equiv T_{M,R,G} \sqrt{(1-A)}.
    ```

## The costs of climate intervention

Coming soon.

## Optimization: what makes a climate "optimal"?

Coming soon.