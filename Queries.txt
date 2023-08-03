SELECT * FROM `covidproject-123.PortfolioProject.CovidDeaths` order by 3, 4

SELECT * FROM `covidproject-123.PortfolioProject.CovidVaccinaton` order by 3, 4


-- Select Data that we are going to be starting with
SELECT location, date, total_cases, new_cases, total_deaths, population FROM `covidproject-123.PortfolioProject.CovidDeaths` order by 1, 2


-- Total Cases vs Total Deaths
-- Shows likelihood of dying if you contract covid in your country
SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage FROM `covidproject-123.PortfolioProject.CovidDeaths` where continent is not null order by 1, 2

SELECT location, date, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage FROM `covidproject-123.PortfolioProject.CovidDeaths` where location like '%states%' and where continent is not null order by 1, 2


-- Total Cases vs Population
-- Shows what percentage of population infected with Covid
SELECT location, date, total_cases, population, (total_cases/population)*100 as PercentOfPopulationInfected FROM `covidproject-123.PortfolioProject.CovidDeaths` where continent is not null order by 1, 2

SELECT location, date, total_cases, population, (total_cases/population)*100 as PercentOfPopulationInfected FROM `covidproject-123.PortfolioProject.CovidDeaths` where location like '%Afghanistan%' and where continent is not null order by 1, 2


-- Countries with Highest Infection Rate compared to Population
SELECT location, population, MAX(total_cases) as HighestInfectionCount, MAX((total_cases/population)*100) as PercentOfPopulationInfected FROM `covidproject-123.PortfolioProject.CovidDeaths` where continent is not null group by population, location order by PercentOfPopulationInfected desc


-- Countries with Highest Death Count per Population
SELECT location, MAX(cast(total_deaths as int)) as TotalDeathCount FROM `covidproject-123.PortfolioProject.CovidDeaths` where continent is not null group by location order by TotalDeathCount desc


-- BREAKING THINGS DOWN BY CONTINENT
-- Showing contintents with the highest death count per population
SELECT continent, MAX(cast(total_deaths as int)) as TotalDeathCount FROM `covidproject-123.PortfolioProject.CovidDeaths` where continent is not null group by continent order by TotalDeathCount desc


-- GLOBAL NUMBERS
SELECT date, total_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage FROM `covidproject-123.PortfolioProject.CovidDeaths` where continent is not null order by 1, 2

SELECT date, SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths,SUM(cast(new_deaths as int))/SUM(new_cases) *100 as DeathPercentage FROM `covidproject-123.PortfolioProject.CovidDeaths` where continent is not null group by date order by 1, 2

SELECT SUM(new_cases) as total_cases, SUM(cast(new_deaths as int)) as total_deaths,SUM(cast(new_deaths as int))/SUM(new_cases) *100 as DeathPercentage FROM `covidproject-123.PortfolioProject.CovidDeaths` where continent is not null group by date order by 1, 2



SELECT * FROM `covidproject-123.PortfolioProject.CovidVaccinaton`


--JOIN OPERATION
SELECT * FROM `covidproject-123.PortfolioProject.CovidVaccinaton` death join `covidproject-123.PortfolioProject.CovidVaccinaton` vac on death.location = vac.location and death.date = vac.date

-- Total Population density vs Vaccinations
SELECT death.continent, death.location, death.date, death.population_density, vac.new_vaccinations FROM `covidproject-123.PortfolioProject.CovidVaccinaton` death join `covidproject-123.PortfolioProject.CovidVaccinaton` vac on death.location = vac.location and death.date = vac.date where death.continent is not null order by 2, 3

SELECT death.continent, death.location, death.date, death.population_density, vac.new_vaccinations, SUM(cast(vac.new_vaccinations as int)) OVER (Partition by death.Location order by death.location, death.date) as RollingPeopleVaccinated FROM `covidproject-123.PortfolioProject.CovidVaccinaton` death join `covidproject-123.PortfolioProject.CovidVaccinaton` vac on death.location = vac.location and death.date = vac.date where death.continent is not null order by 2, 3


--USE CTE
With PopvsVac (Continent, Location, Date, Population_density, new_Vaccinations, RollingPeopleVaccinated)
as
(
Select death.continent, death.location, death.date, death.population_density, vac.new_vaccinations
, SUM(CAST(vac.new_vaccinations as int)) OVER (Partition by death.Location Order by death.location, death.Date) as RollingPeopleVaccinated
From `covidproject-123.PortfolioProject.CovidVaccinaton` death
Join `covidproject-123.PortfolioProject.CovidVaccinaton` vac
	On death.location = vac.location
	and death.date = vac.date
where death.continent is not null 
--order by 2,3
)
Select *, (RollingPeopleVaccinated/Population_density)*100
From PopvsVac


--TEMP TABLE
DROP Table if exists `covidproject-123.PortfolioProject.PercentPopulationdensityVaccinated`
CREATE TABLE `covidproject-123.PortfolioProject.PercentPopulationdensityVaccinated`
(
  Continent string(255),
  Location string(255),
  Date datetime,
  Population_density numeric,
  New_vaccinations numeric,
  RollingPeopleVaccinated numeric
)
INSERT INTO `covidproject-123.PortfolioProject.PercentPopulationdensityVaccinated`
SELECT death.continent, death.location, death.date, death.population_density, vac.new_vaccinations
, SUM(CAST(vac.new_vaccinations as int)) OVER (Partition by death.Location Order by death.location, death.Date) as RollingPeopleVaccinated
From `covidproject-123.PortfolioProject.CovidVaccinaton` death
Join `covidproject-123.PortfolioProject.CovidVaccinaton` vac
	On death.location = vac.location
	and death.date = vac.date
--where death.continent is not null 
--order by 2,3

SELECT *, (RollingPeopleVaccinated/Population_density)*100
From `covidproject-123.PortfolioProject.PercentPopulationdensityVaccinated`


--CREATE VIEW
CREATE VIEW `covidproject-123.PortfolioProject.PercentPopulationdensityVaccinated1` as 
SELECT death.continent, death.location, death.date, death.population_density, vac.new_vaccinations
, SUM(CAST(vac.new_vaccinations as int)) OVER (Partition by death.Location Order by death.location, death.Date) as RollingPeopleVaccinated
From `covidproject-123.PortfolioProject.CovidVaccinaton` death
Join `covidproject-123.PortfolioProject.CovidVaccinaton` vac
	On death.location = vac.location
	and death.date = vac.date
where death.continent is not null 

select * from `covidproject-123.PortfolioProject.PercentPopulationdensityVaccinated1`