-- 1. GLOBAL NUMBERS

Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
From `covidproject-123.PortfolioProject.CovidDeaths`
where continent is not null 
order by 1,2

-- THIS CAN ALSO BE WRITTEN AS


Select SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths, SUM(cast(new_deaths as int))/SUM(New_Cases)*100 as DeathPercentage
From `covidproject-123.PortfolioProject.CovidDeaths`
where location = 'World'
order by 1,2


-- 2. TOTAL DEATHS PER CATEGORY

Select location, SUM(cast(new_deaths as int)) as TotalDeathCount
From `covidproject-123.PortfolioProject.CovidDeaths`
Where continent is null 
and location not in ('World', 'European Union', 'International')
Group by location
order by TotalDeathCount desc


-- 3. PERCENT POPULATION INFECTED PER COUNTRY

Select location, population, MAX(total_cases) as HighestInfectionCount,  Max((total_cases/population))*100 as PercentPopulationInfected
From `covidproject-123.PortfolioProject.CovidDeaths`
Group by Location, Population
order by PercentPopulationInfected desc


-- 4. PERCENT POPULATION INFECTED

Select location, population, date, MAX(total_cases) as HighestInfectionCount,  (Max(total_cases)/population)*100 as PercentPopulationInfected
From `covidproject-123.PortfolioProject.CovidDeaths`
Group by location, population, date
order by PercentPopulationInfected desc








