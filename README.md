# La géodésique — Geodesic Calculator on an Ellipsoid

A Django web application that solves the **direct** and **inverse** geodesic
problems on a reference ellipsoid using **Vincenty's formulae**, and renders the
resulting geodesic curve in an interactive 3D plot of the chosen ellipsoid.

The whole interface is in French (originally a school project, *La géodésique*
≈ *the geodesic*).

## What the app actually computes

A *geodesic* is the shortest path between two points on the surface of an
ellipsoid (the "straight line" of a curved surface). Geodesy traditionally
poses two questions about it, and this app answers both:

### Direct problem (`/appgeo/direct/`)

> **Given** a starting point `(φ₁, λ₁)`, an initial azimuth `α₁`, and a
> distance `s` — **find** the end point `(φ₂, λ₂)` and the reverse azimuth `α₂`.

Use it to answer questions like *"if I start in Casablanca and travel 5000 km
on a bearing of 60°, where do I end up?"*

### Inverse problem (`/appgeo/inverse/`)

> **Given** two points `(φ₁, λ₁)` and `(φ₂, λ₂)` — **find** the geodesic
> distance `s` between them and the two azimuths `α₁₂` (forward) and `α₂₁`
> (reverse).

Use it to answer *"what is the shortest surface distance from Paris to Tokyo,
and in what direction do I leave Paris?"*

Both problems are solved iteratively with the
[Vincenty formulae](https://en.wikipedia.org/wiki/Vincenty%27s_formulae),
which are accurate to within a fraction of a millimetre on an Earth-sized
ellipsoid.

## What you can configure

- **Reference ellipsoid** — pick from 18 commonly used ellipsoids (WGS 84, WGS
  72, GRS 1980, Krassovsky 1940, International, Clarke 1866 / 1880, Bessel
  1841, Airy 1830, Everest 1830, …) or define your own with custom semi-major
  and semi-minor axes `(a, b)`.
- **Input coordinate system**:
  - **Géodésiques** — latitude and longitude in degrees / minutes / seconds,
    with N/S and E/W hemisphere selectors.
  - **Géocentriques** — Cartesian `(X, Y, Z)` in metres; the app converts to
    geodetic coordinates via an iterative ellipsoidal projection.
- **Hemisphere signs** for each angle, so you don't have to manually negate
  southern / western inputs.

## What you get back

For both problems the result page shows the numerical answer (coordinates,
distance, azimuths) **and** an interactive 3D Plotly figure containing:

- the chosen ellipsoid as a coloured surface,
- the geodesic curve drawn on the surface (start point in yellow, end point
  in green),
- reference meridians (Greenwich and anti-Greenwich), the equator, and
  parallels every 10° for orientation.

You can pan, zoom, and rotate the figure directly in the browser.

## Project layout

```
ProjetGeo-main/
├── manage.py                    # Django entry point
├── géodésie/                    # project (settings, root URL conf)
│   ├── settings.py
│   └── urls.py                  # mounts /appgeo/ and /admin/
└── appgeo/                      # the geodesic app
    ├── urls.py                  # /appgeo/{acceuil,direct,inverse,resultd,resul}/
    ├── views.py                 # request handling and unit conversion
    ├── direct.py                # Vincenty direct formula
    ├── innverse.py              # Vincenty inverse formula
    ├── geocentrique2geodesique.py  # X,Y,Z → φ,λ,h conversion
    ├── ploot.py                 # Plotly 3D ellipsoid + geodesic rendering
    └── template/                # HTML templates (French UI)
        ├── base.html
        ├── acceuil.html         # landing page with rotating globe
        ├── direct.html          # direct-problem form
        ├── inverse.html         # inverse-problem form
        ├── result.html
        └── resultinv.html
```

## Running it locally

Requires Python 3.10+ (tested on 3.14). From the repo root:

```powershell
# install dependencies
pip install django plotly numpy

# run the dev server
python ProjetGeo-main/manage.py runserver
```

Then open <http://127.0.0.1:8000/appgeo/acceuil/> for the home page, which
links to the two solver pages.

A `Pipfile` is included if you prefer pipenv:

```powershell
pipenv install
pipenv run python ProjetGeo-main/manage.py runserver
```

## URL map

| Path                    | View          | Purpose                                  |
|-------------------------|---------------|------------------------------------------|
| `/appgeo/acceuil/`      | `acceuil`     | Landing page (rotating globe + 2 links)  |
| `/appgeo/direct/`       | `say_hello`   | Direct-problem input form                |
| `/appgeo/resultd/`      | `add`         | Direct-problem result + 3D plot          |
| `/appgeo/inverse/`      | `say_hello2`  | Inverse-problem input form               |
| `/appgeo/resul/`        | `inverse`     | Inverse-problem result + 3D plot         |
| `/admin/`               | Django admin  | Standard Django admin (unused)           |

## Math, briefly

For the **direct** problem on an ellipsoid with flattening `f`, the algorithm
projects the geodesic onto an auxiliary sphere via the *reduced latitude*
`β = atan((1−f) tan φ)`, computes the angular distance with Vincenty's series
expansion in `A`, `B` and the auxiliary `σ`, and iterates until `σ` converges.
The end-point latitude, longitude difference, and reverse azimuth are then
read off in closed form.

The **inverse** problem iterates on the longitude difference `Δλ` on the
auxiliary sphere until it agrees with the ellipsoidal longitude difference,
then evaluates `s = b · A · (σ − Δσ)` and the two azimuths.

The `geocentrique2geodesique` helper inverts the standard parametric mapping
`(X, Y, Z) → (φ, λ, h)` by Newton iteration on `φ`, used to let users supply
ECEF coordinates instead of degrees.
