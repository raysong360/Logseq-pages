```julia
using ModelingToolkit, DifferentialEquations
```


```julia
@parameters t σ ρ β
# ρ: \rho
```




$$ \begin{equation}
\left[
\begin{array}{c}
t \\
\sigma \\
\rho \\
\beta \\
\end{array}
\right]
\end{equation}
 $$




```julia
@variables x(t) y(t) z(t)
```




$$ \begin{equation}
\left[
\begin{array}{c}
x\left( t \right) \\
y\left( t \right) \\
z\left( t \right) \\
\end{array}
\right]
\end{equation}
 $$




```julia
D = Differential(t)
D(x)
```




$$ \begin{equation}
\frac{\mathrm{d} x\left( t \right)}{\mathrm{d}t}
\end{equation}
 $$




```julia
eqs = [D(D(x)) ~ σ * (y-x),
       D(y) ~ x*(ρ-z) - y,
       D(z) ~ x*y - β*z]
```




$$ \begin{align}
\frac{\mathrm{d}}{\mathrm{d}t} \frac{\mathrm{d} x\left( t \right)}{\mathrm{d}t} &= \left(  - x\left( t \right) + y\left( t \right) \right) \sigma \\
\frac{\mathrm{d} y\left( t \right)}{\mathrm{d}t} &=  - y\left( t \right) + x\left( t \right) \left(  - z\left( t \right) + \rho \right) \\
\frac{\mathrm{d} z\left( t \right)}{\mathrm{d}t} &= x\left( t \right) y\left( t \right) - z\left( t \right) \beta
\end{align}
 $$




```julia
# Define the ODE system
@named sys = ODESystem(eqs, t, [x,y,z], [σ,ρ,β])

# The turtorial vedio is the old version. 
# `sys = ODESystem(eqs)` is no longer appliable
```




$$ \begin{align}
\frac{\mathrm{d}}{\mathrm{d}t} \frac{\mathrm{d} x\left( t \right)}{\mathrm{d}t} &= \left(  - x\left( t \right) + y\left( t \right) \right) \sigma \\
\frac{\mathrm{d} y\left( t \right)}{\mathrm{d}t} &=  - y\left( t \right) + x\left( t \right) \left(  - z\left( t \right) + \rho \right) \\
\frac{\mathrm{d} z\left( t \right)}{\mathrm{d}t} &= x\left( t \right) y\left( t \right) - z\left( t \right) \beta
\end{align}
 $$




```julia
# You can check the equations by this
equations(sys)
```




$$ \begin{align}
\frac{\mathrm{d}}{\mathrm{d}t} \frac{\mathrm{d} x\left( t \right)}{\mathrm{d}t} &= \left(  - x\left( t \right) + y\left( t \right) \right) \sigma \\
\frac{\mathrm{d} y\left( t \right)}{\mathrm{d}t} &=  - y\left( t \right) + x\left( t \right) \left(  - z\left( t \right) + \rho \right) \\
\frac{\mathrm{d} z\left( t \right)}{\mathrm{d}t} &= x\left( t \right) y\left( t \right) - z\left( t \right) \beta
\end{align}
 $$




```julia
# sys = ode_order_lowering(sys)
## Old fashion! Actually no need this anymore.
## Use `structural_simplify` to realize both simplify and complete functions
```




$$ \begin{align}
\frac{\mathrm{d} xˍt\left( t \right)}{\mathrm{d}t} &= \left(  - x\left( t \right) + y\left( t \right) \right) \sigma \\
\frac{\mathrm{d} y\left( t \right)}{\mathrm{d}t} &=  - y\left( t \right) + x\left( t \right) \left(  - z\left( t \right) + \rho \right) \\
\frac{\mathrm{d} z\left( t \right)}{\mathrm{d}t} &= x\left( t \right) y\left( t \right) - z\left( t \right) \beta \\
\frac{\mathrm{d} x\left( t \right)}{\mathrm{d}t} &= xˍt\left( t \right)
\end{align}
 $$




```julia
# Initial conditions of variables and the derivative
# For second-order or higher ODEs, the derivative should have initials
u0 = [D(x) => 2.0,
      x => 1.0,
      y => 0.0,
      z => 0.0]
```




    4-element Vector{Pair{Num, Float64}}:
     Differential(t)(x(t)) => 2.0
                      x(t) => 1.0
                      y(t) => 0.0
                      z(t) => 0.0




```julia
# Initial conditions of parameters
p0 = [σ => 28.0,
      ρ => 10.0,
      β => 8/3]
```




    3-element Vector{Pair{Num, Float64}}:
     σ => 28.0
     ρ => 10.0
     β => 2.6666666666666665




```julia
# time span
tspan = (0., 100.)
```




    (0.0, 100.0)




```julia
# Define the ODE problem
prob = ODEProblem(sys, u0, tspan, p0, jac=true, sparse=true)

# Error! Must `complete` or `Structural_simplify`
```




    [38;2;86;182;194mODEProblem[0m with uType [38;2;86;182;194mVector{Float64}[0m and tType [38;2;86;182;194mFloat64[0m. In-place: [38;2;86;182;194mtrue[0m
    timespan: (0.0, 100.0)
    u0: 4-element Vector{Float64}:
     2.0
     0.0
     0.0
     1.0




```julia
# Simplify and examine the ODE system (Required)

sys = structural_simplify(sys)
```




$$ \begin{align}
\frac{\mathrm{d} xˍt\left( t \right)}{\mathrm{d}t} &= \left(  - x\left( t \right) + y\left( t \right) \right) \sigma \\
\frac{\mathrm{d} y\left( t \right)}{\mathrm{d}t} &=  - y\left( t \right) + x\left( t \right) \left(  - z\left( t \right) + \rho \right) \\
\frac{\mathrm{d} z\left( t \right)}{\mathrm{d}t} &= x\left( t \right) y\left( t \right) - z\left( t \right) \beta \\
\frac{\mathrm{d} x\left( t \right)}{\mathrm{d}t} &= xˍt\left( t \right)
\end{align}
 $$




```julia
# Define the ODE problem

prob = ODEProblem(sys, u0, tspan, p0, jac=true, sparse=true)
```




    [38;2;86;182;194mODEProblem[0m with uType [38;2;86;182;194mVector{Float64}[0m and tType [38;2;86;182;194mFloat64[0m. In-place: [38;2;86;182;194mtrue[0m
    timespan: (0.0, 100.0)
    u0: 4-element Vector{Float64}:
     2.0
     0.0
     0.0
     1.0




```julia
# Solve the ODE problem

sol = solve(prob, Tsit5())
```




    retcode: Success
    Interpolation: specialized 4th order "free" interpolation
    t: 1495-element Vector{Float64}:
       0.0
       0.00014131524176735154
       0.0015544676594408668
       0.012956381792766277
       0.038266700044138006
       0.07852291796751046
       0.12210864065288088
       0.17155375443120538
       0.2311290630460472
       0.30252383872495503
       0.38386217303396797
       0.4740503441504558
       0.5734250423398977
       ⋮
      99.32332883522889
      99.38925553120134
      99.44426737492837
      99.50500396864174
      99.57665667367455
      99.64283640514518
      99.72166966002771
      99.79967562312255
      99.86801143860899
      99.93304884850775
      99.99013919862291
     100.0
    u: 1495-element Vector{Vector{Float64}}:
     [2.0, 0.0, 0.0, 1.0]
     [1.9960454103704428, 0.0014132521265988688, 9.986094350108851e-8, 1.0002823510089436]
     [1.956746201469907, 0.01555657123298723, 1.2096178746316906e-5, 1.0030752466310615]
     [1.6563910957343146, 0.13029732534611174, 0.0008464888325952026, 1.023645017393648]
     [1.1011942420041492, 0.3874130558030291, 0.007445311135321613, 1.0582098436702096]
     [0.5557298463522201, 0.7959541011040254, 0.031202921104640704, 1.0901348104540605]
     [0.44950102412404824, 1.2290078635296977, 0.07390400544865937, 1.1102092717261387]
     [0.9266726317320978, 1.706238166829207, 0.14159577334379558, 1.1416871365805596]
     [2.278100596811044, 2.277379327138541, 0.2511409426706846, 1.2331771082429113]
     [4.867419855792262, 3.0146935935180124, 0.440479944814211, 1.482457057983321]
     [8.915714699411796, 4.051098696490123, 0.8073301342326066, 2.0355655920517335]
     [14.6576861410621, 5.619326307780356, 1.6281502019928793, 3.0887972059015723]
     [22.36790539860828, 7.8601790476306155, 3.6281890210441676, 4.918653364888541]
     ⋮
     [-6.15981151102123, -2.842342706335567, 14.742776747396606, -17.991491306134918]
     [26.490554853784563, 1.965328513110026, 12.489152578699695, -17.36522586476527]
     [55.49566888001103, 2.3776742152547077, 8.723147703807076, -15.09684204259026]
     [80.19625687471111, 0.22319498068508795, 6.37799845949672, -10.921012381251876]
     [93.91126047193669, -1.9435112337381444, 5.714426570727498, -4.58090729495291]
     [92.47053186268245, -2.212884521963285, 4.966589670343762, 1.6531178052235893]
     [78.34378276619664, 0.2736929875097288, 3.6720087645276114, 8.448758815092065]
     [59.619887226999516, 5.2078873509076935, 5.364596386123238, 13.834326658989282]
     [42.066087715087576, 6.848721336824774, 10.923029356999272, 17.327760613534355]
     [17.74636186472372, 2.5614624216728306, 14.710142641007776, 19.336065831253627]
     [-13.425204706784038, -1.9987153748280382, 12.635789734326266, 19.49627693453904]
     [-19.399440521584744, -2.411052627710416, 11.888639576409144, 19.334495160672322]




```julia
using Plots
```


```julia
# To see the relationship between x and y (plot y(t) against x(t))

plot(sol, idxs=(x, y), label="y vs x", xlabel="x(t)", ylabel="y(t)")
```

![](https://i.imgur.com/eBnZrp7.png)


```julia
# To see how z changes with x and y (plot z(t) against x(t), y(t))

plot(sol, idxs=(x, y, z), label="z vs x and y", xlabel="x(t)", ylabel="y(t)", zlabel="z(t)")
```

![](https://i.imgur.com/suLq6fv.png)


```julia
# To Check if tspan is resonable or if x(t) stabilizes
```


```julia
# To see how x(t) varies with t

plot(sol, idxs=x)  # Check if x(t) stabilizes
```

![](https://i.imgur.com/i7wKyQe.png)


```julia
# To see how x(t), y(t) varies with t
plot(sol, idxs=[x, y], label=["x(t)" "y(t)"], xlabel="Time (t)", ylabel="Value")
```

![](https://i.imgur.com/6Du51mH.png)


```julia
# To see how D(x(t)) varies with t

plot(sol, idxs=D(x)) 
```

![](https://i.imgur.com/hbL9kYN.png)


```julia
# To see how D(D(x(t))) varies with t

plot(sol, idxs=D(D(x)))  # Check if x(t) stabilizes
#= Error occurs since second derivatives like D(D(x)) aren't automatically included in the solution
   If you want to see it, you need to define a variable v(t) and have equation D(x) ~ t in the system
=#
```


    ArgumentError: Collection is empty, must contain exactly 1 element

    

    Stacktrace:

     [1] only(x::Set{Any})

       @ Base.Iterators .\iterators.jl:1523

     [2] macro expansion

       @ C:\Users\rzhsong\.julia\packages\SciMLBase\EiBzT\src\solutions\solution_interface.jl:195 [inlined]

     [3] apply_recipe(plotattributes::AbstractDict{Symbol, Any}, sol::SciMLBase.AbstractTimeseriesSolution)

       @ SciMLBase C:\Users\rzhsong\.julia\packages\RecipesBase\BRe07\src\RecipesBase.jl:300

     [4] _process_userrecipes!(plt::Any, plotattributes::Any, args::Any)

       @ RecipesPipeline C:\Users\rzhsong\.julia\packages\RecipesPipeline\BGM3l\src\user_recipe.jl:38

     [5] recipe_pipeline!(plt::Any, plotattributes::Any, args::Any)

       @ RecipesPipeline C:\Users\rzhsong\.julia\packages\RecipesPipeline\BGM3l\src\RecipesPipeline.jl:72

     [6] _plot!(plt::Plots.Plot, plotattributes::Any, args::Any)

       @ Plots C:\Users\rzhsong\.julia\packages\Plots\kLeqV\src\plot.jl:223

     [7] plot(args::Any; kw...)

       @ Plots C:\Users\rzhsong\.julia\packages\Plots\kLeqV\src\plot.jl:102

     [8] top-level scope

       @ In[68]:3



```julia
# To see some key information from solver diagnostics
sol.destats

# Number of accepted >> rejected steps , usually meaning appropriately
```




    SciMLBase.DEStats
    Number of function 1 evaluations:                  9291
    Number of function 2 evaluations:                  0
    Number of W matrix evaluations:                    0
    Number of linear solves:                           0
    Number of Jacobians created:                       0
    Number of nonlinear solver iterations:             0
    Number of nonlinear solver convergence failures:   0
    Number of fixed-point solver iterations:                     0
    Number of fixed-point solver convergence failures:           0
    Number of rootfind condition calls:                0
    Number of accepted steps:                          1494
    Number of rejected steps:                          54




```julia
# To Check if tspan is resonable or if x(t) stabilizes
# We can shorten / increase the tspan and then see the solution
# For distinguishment, 1 is added

using ModelingToolkit, DifferentialEquations

@parameters t σ ρ β
@variables x(t) y(t) z(t)

D = Differential(t) 
D(x)

eqs1 = [D(D(x)) ~ σ * (y-x),
       D(y) ~ x*(ρ-z) - y,
       D(z) ~ x*y - β*z]

@named sys1 = ODESystem(eqs1, t, [x,y,z], [σ,ρ,β])
sys1 = structural_simplify(sys1)

u0 = [D(x) => 2.0,
      x => 1.0,
      y => 0.0,
      z => 0.0]

p0 = [σ => 28.0,
      ρ => 10.0,
      β => 8/3]

tspan1 = (0., 5.)

prob1 = ODEProblem(sys1, u0, tspan1, p0, jac=true, sparse=true)

sol1 = solve(prob1, Tsit5())
```




    retcode: Success
    Interpolation: specialized 4th order "free" interpolation
    t: 74-element Vector{Float64}:
     0.0
     0.00014131524176735154
     0.0015544676594408668
     0.012956381792766277
     0.038266700044138006
     0.07852291796751046
     0.12210864065288088
     0.17155375443120538
     0.2311290630460472
     0.30252383872495503
     0.38386217303396797
     0.4740503441504558
     0.5734250423398977
     ⋮
     4.289894250618254
     4.355140742248154
     4.426635613819042
     4.520066264037508
     4.59195701253417
     4.662281591396237
     4.726266018237121
     4.790183640037156
     4.842892608523908
     4.909048916341191
     4.971592625758713
     5.0
    u: 74-element Vector{Vector{Float64}}:
     [2.0, 0.0, 0.0, 1.0]
     [1.9960454103704428, 0.0014132521265988688, 9.986094350108851e-8, 1.0002823510089436]
     [1.956746201469907, 0.01555657123298723, 1.2096178746316906e-5, 1.0030752466310615]
     [1.6563910957343146, 0.13029732534611174, 0.0008464888325952026, 1.023645017393648]
     [1.1011942420041492, 0.3874130558030291, 0.007445311135321613, 1.0582098436702096]
     [0.5557298463522201, 0.7959541011040254, 0.031202921104640704, 1.0901348104540605]
     [0.44950102412404824, 1.2290078635296977, 0.07390400544865937, 1.1102092717261387]
     [0.9266726317320978, 1.706238166829207, 0.14159577334379558, 1.1416871365805596]
     [2.278100596811044, 2.277379327138541, 0.2511409426706846, 1.2331771082429113]
     [4.867419855792262, 3.0146935935180124, 0.440479944814211, 1.482457057983321]
     [8.915714699411796, 4.051098696490123, 0.8073301342326066, 2.0355655920517335]
     [14.6576861410621, 5.619326307780356, 1.6281502019928793, 3.0887972059015723]
     [22.36790539860828, 7.8601790476306155, 3.6281890210441676, 4.918653364888541]
     ⋮
     [88.49736336445125, -1.4747949620196483, 5.964306534150299, -9.151891078269191]
     [95.43420631525257, -2.9540107728946743, 5.8300490988075815, -3.0762235557609645]
     [88.91101486424327, -2.601310784536183, 4.753310141192113, 3.5892657229139626]
     [67.38318718425208, 1.8113351666882789, 3.493603983165983, 10.950136250453934]
     [49.52050161957271, 6.435763643008754, 6.761186821688674, 15.145370068328567]
     [30.14229668746499, 6.003563641968732, 12.921955152601193, 17.983690711904732]
     [2.884795011119915, 0.6071271065240837, 14.601481104669533, 19.10418798454243]
     [-33.53937246571422, -2.7757168398585423, 10.58383503017626, 18.14858616236231]
     [-62.170101194814755, -1.5473874915394452, 7.193329426920635, 15.601393498878833]
     [-86.57195725320751, 1.6006775915092544, 6.035593632036406, 10.597154491589242]
     [-95.6201494954555, 3.3194150456444054, 6.219740522741758, 4.831226586380392]
     [-95.61053483189372, 3.5966678433666117, 6.093409144703363, 2.1093968917168806]



![](https://i.imgur.com/ZXrdpKf.png)


```julia
plot(sol1, idxs=(D(x)))
```

![](https://i.imgur.com/DjkZkRN.png)


```julia
# seemingly tspan = (0., 10.) is enough
```
