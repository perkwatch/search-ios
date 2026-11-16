# BuckThem Search — iOS shell

Capacitor shell over https://search.buckthem.com/ (the live web product; the app loads the live
surface — no copied content). Bundle ID `com.buckthem.search`.

Built by the dispatch-only workflow `build-search.yml` in
perkwatch/perkwatch-ios on the self-hosted Tylers-iMac runner (the estate
TestFlight pipeline). Modes: compile = unsigned build check; release =
archive + TestFlight upload (release gates on the App Store Connect app
record existing — creating that record is the account holder's portal step).

The committed CURRENT_PROJECT_VERSION base (in ios/App/project.yml) must
equal the last uploaded TestFlight build number; bookkeeping commits that
advance it carry [skip ci].
