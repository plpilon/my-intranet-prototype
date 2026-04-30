<!-- Search Bar -->
                <section id="wb-srch" class="col-lg-offset-4 col-md-offset-4 col-sm-offset-2 col-xs-12 col-sm-5 col-md-4">
                    <h2>Search</h2>
                    
                    <!-- Dynamically route to /search/ or /fr/search/ -->
                    {% set search_path = 'search/' %}
                    {% if i18n_page_locale != 'en' %}
                        {% set search_path = i18n_page_locale ~ '/search/' %}
                    {% endif %}
                    
                    <form action="{{ config.site_url }}{{ search_path }}" method="get" name="cse-search-box" role="search">
                        <div class="form-group wb-srch-qry">
                            <label for="wb-srch-q" class="wb-inv">Search website</label>
                            <!-- Notice the name="q" attribute. This passes the query to the URL -->
                            <input id="wb-srch-q" list="wb-srch-q-ac" class="wb-srch-q form-control" name="q" type="search" value="" size="34" maxlength="170" placeholder="Search" />
                            <datalist id="wb-srch-q-ac"></datalist>
                        </div>
                        <div class="form-group submit">
                            <button type="submit" id="wb-srch-sub" class="btn btn-primary btn-small" name="wb-srch-sub">
                                <span class="glyphicon-search glyphicon"></span>
                                <span class="wb-inv">Search</span>
                            </button>
                        </div>
                    </form>
                </section>
