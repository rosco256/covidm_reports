
-include ../local.makefile

# TODO start passing these in from linking makefile
COVIDMPATH ?= ../../covidm
DATADIR ?= ~/Dropbox/covidm_reports
INTINPUTDIR := ${DATADIR}/hpc_inputs
HPCDIR ?= ~/Dropbox/covidm_hpc_output

../utils/%:
	cd $(@D) && $(MAKE) $(@F)

# also depends on other bits, but 001 guaranteed to change if
# run sim has changed
${HPCDIR}/%/peak.qs: digest.R ${HPCDIR}/%/001.qs ../utils/sharedconst.rda
	Rscript $^ $@

${HPCDIR}/%/accs.qs ${HPCDIR}/%/alls.qs: ${HPCDIR}/%/peak.qs

${HPCDIR}/%/peak_low.qs: digest_low.R ${HPCDIR}/%/001_low.qs ../utils/sharedconst.rda
	Rscript $^ $@

${HPCDIR}/%/accs_low.qs ${HPCDIR}/%/alls_low.qs: ${HPCDIR}/%/peak_low.qs

${HPCDIR}/%_low.qs: run_scenarios_low.R
	mkdir -p $(@D)
	Rscript $^ ${COVIDMPATH} $(subst /, ,$*) ${INTINPUTDIR} $@

DETDIR ?= ~/Dropbox/covidm_hpc_detail
DETINPUTDIR := ${DATADIR}/hpc_detailed

${DETDIR}/%/peak_detail.qs: digest_detail.R ${DETDIR}/%/001_detail.qs ../utils/sharedconst.rda
	Rscript $^ $@

${DETDIR}/%/accs_detail.qs ${DETDIR}/%/alls_detail.qs: ${DETDIR}/%/peak_detail.qs

${DETDIR}/%_detail.qs: run_scenarios_detail.R
	mkdir -p $(@D)
	Rscript $^ ${COVIDMPATH} $(subst /, ,$*) ${DETINPUTDIR} $@

${DETDIR}/%/peak_detail_low.qs: digest_detail_low.R ${DETDIR}/%/001_detail_low.qs ../utils/sharedconst.rda
	Rscript $^ $@

${DETDIR}/%/accs_detail_low.qs ${DETDIR}/%/alls_detail_low.qs: ${DETDIR}/%/peak_detail_low.qs

${DETDIR}/%_detail_low.qs: run_scenarios_detail_low.R
	mkdir -p $(@D)
	Rscript $^ ${COVIDMPATH} $(subst /, ,$*) ${DETINPUTDIR} $@



${HPCDIR}/%.qs: run_scenarios.R
	mkdir -p $(@D)
	Rscript $^ ${COVIDMPATH} $(subst /, ,$*) ${INTINPUTDIR} $@

SIMRUNS ?= 2

# if asking local examples; uses % length to differentiate between local
# and HPC runs
%/peak.qs: digest.R ${HPCDIR}/%/001.qs ../utils/sharedconst.rda
	Rscript $^ $@
	
%/accs.qs %/alls.qs: %/peak.qs

%.qs: run_scenarios.R
	@mkdir -p $(@D)
	SIMRUNS=${SIMRUNS} Rscript $^ ${COVIDMPATH} $(subst /, ,$*) ${INTINPUTDIR} $@

# TODO: this depends on the all countries, peak|accs|001.qs
${HPCDIR}/summary.tar.gz:
	cd $(@D) && tar -czvf $(@F) */peak.qs */accs.qs */alls.qs */001.qs

# TODO: this depends on the all countries, peak|accs|001.qs
${HPCDIR}/summary_low.tar.gz:
	cd $(@D) && tar -czvf $(@F) */peak_low.qs */accs_low.qs */alls_low.qs */001_low.qs
