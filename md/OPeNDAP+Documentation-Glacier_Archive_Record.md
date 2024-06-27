<font size="2">

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<gar:GlacierArchiveRecord
        xmlns:gar="http://xml.opendap.org/ns/aws/glacier/ArchiveRecord/01#"
        resourceId="/data/nc/coads_climatology.nc"
        vault="opendap-test"
        archiveId="XIfGXl0qWkfDVbKL3Ibb6XQ8OBC44YwOBGl9ds-yA18HN8zDOBi1A2ZugWprFHgwL-8LRQLSfz8BqoJLaMdaflPgM-J-_D0gHQwS_CDHXp5R0vHUADuZKd49oP4eir2qw508EJ57kQ">

    <Dataset
        name="coads_climatology.nc"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:grddl="http://www.w3.org/2003/g/data-view#"
        grddl:transformation="http://xml.opendap.org/transforms/ddxToRdfTriples.xsl"
        ns="http://xml.opendap.org/ns/DAP/3.2#"
        xmlns:dap="http://xml.opendap.org/ns/DAP/3.2#"
        dapVersion="3.2"
        xmlns:xml="http://www.w3.org/XML/1998/namespace"
        xml:base="http://localhost:8080/opendap/hyrax/data/nc/coads_climatology.nc">
        <Attribute name="NC_GLOBAL" type="Container">
            <Attribute name="history" type="String">
                <value>FERRET V4.30 (debug/no GUI) 15-Aug-96</value>
            </Attribute>
        </Attribute>
        <Attribute name="DODS_EXTRA" type="Container">
            <Attribute name="Unlimited_Dimension" type="String">
                <value>TIME</value>
            </Attribute>
        </Attribute>
        <Array name="TIME">
            <Float64 name="TIME"/>
            <dimension name="TIME" size="12"/>
            <value>366</value> <value>1096.485</value> <value>1826.97</value> <value>2557.455</value> <value>3287.94</value> <value>4018.425</value> <value>4748.91</value> <value>5479.395</value> <value>6209.88</value> <value>6940.365</value> <value>7670.85</value> <value>8401.335</value>
        </Array>
        <Array name="COADSY">
            <Float64 name="COADSY"/>
            <dimension name="COADSY" size="90"/>
            <value>-89</value> <value>-87</value> <value>-85</value> <value>-83</value> <value>-81</value> <value>-79</value> <value>-77</value> <value>-75</value> <value>-73</value> <value>-71</value> <value>-69</value> <value>-67</value> <value>-65</value> <value>-63</value> <value>-61</value> <value>-59</value> <value>-57</value> <value>-55</value> <value>-53</value> <value>-51</value> <value>-49</value> <value>-47</value> <value>-45</value> <value>-43</value> <value>-41</value> <value>-39</value> <value>-37</value> <value>-35</value> <value>-33</value> <value>-31</value> <value>-29</value> <value>-27</value> <value>-25</value> <value>-23</value> <value>-21</value> <value>-19</value> <value>-17</value> <value>-15</value> <value>-13</value> <value>-11</value> <value>-9</value> <value>-7</value> <value>-5</value> <value>-3</value> <value>-1</value> <value>1</value> <value>3</value> <value>5</value> <value>7</value> <value>9</value> <value>11</value> <value>13</value> <value>15</value> <value>17</value> <value>19</value> <value>21</value> <value>23</value> <value>25</value> <value>27</value> <value>29</value> <value>31</value> <value>33</value> <value>35</value> <value>37</value> <value>39</value> <value>41</value> <value>43</value> <value>45</value> <value>47</value> <value>49</value> <value>51</value> <value>53</value> <value>55</value> <value>57</value> <value>59</value> <value>61</value> <value>63</value> <value>65</value> <value>67</value> <value>69</value> <value>71</value> <value>73</value> <value>75</value> <value>77</value> <value>79</value> <value>81</value> <value>83</value> <value>85</value> <value>87</value> <value>89</value>
        </Array>
        <Array name="COADSX">
            <Float64 name="COADSX"/>
            <dimension name="COADSX" size="180"/>
            <value>21</value> <value>23</value> <value>25</value> <value>27</value> <value>29</value> <value>31</value> <value>33</value> <value>35</value> <value>37</value> <value>39</value> <value>41</value> <value>43</value> <value>45</value> <value>47</value> <value>49</value> <value>51</value> <value>53</value> <value>55</value> <value>57</value> <value>59</value> <value>61</value> <value>63</value> <value>65</value> <value>67</value> <value>69</value> <value>71</value> <value>73</value> <value>75</value> <value>77</value> <value>79</value> <value>81</value> <value>83</value> <value>85</value> <value>87</value> <value>89</value> <value>91</value> <value>93</value> <value>95</value> <value>97</value> <value>99</value> <value>101</value> <value>103</value> <value>105</value> <value>107</value> <value>109</value> <value>111</value> <value>113</value> <value>115</value> <value>117</value> <value>119</value> <value>121</value> <value>123</value> <value>125</value> <value>127</value> <value>129</value> <value>131</value> <value>133</value> <value>135</value> <value>137</value> <value>139</value> <value>141</value> <value>143</value> <value>145</value> <value>147</value> <value>149</value> <value>151</value> <value>153</value> <value>155</value> <value>157</value> <value>159</value> <value>161</value> <value>163</value> <value>165</value> <value>167</value> <value>169</value> <value>171</value> <value>173</value> <value>175</value> <value>177</value> <value>179</value> <value>181</value> <value>183</value> <value>185</value> <value>187</value> <value>189</value> <value>191</value> <value>193</value> <value>195</value> <value>197</value> <value>199</value> <value>201</value> <value>203</value> <value>205</value> <value>207</value> <value>209</value> <value>211</value> <value>213</value> <value>215</value> <value>217</value> <value>219</value> <value>221</value> <value>223</value> <value>225</value> <value>227</value> <value>229</value> <value>231</value> <value>233</value> <value>235</value> <value>237</value> <value>239</value> <value>241</value> <value>243</value> <value>245</value> <value>247</value> <value>249</value> <value>251</value> <value>253</value> <value>255</value> <value>257</value> <value>259</value> <value>261</value> <value>263</value> <value>265</value> <value>267</value> <value>269</value> <value>271</value> <value>273</value> <value>275</value> <value>277</value> <value>279</value> <value>281</value> <value>283</value> <value>285</value> <value>287</value> <value>289</value> <value>291</value> <value>293</value> <value>295</value> <value>297</value> <value>299</value> <value>301</value> <value>303</value> <value>305</value> <value>307</value> <value>309</value> <value>311</value> <value>313</value> <value>315</value> <value>317</value> <value>319</value> <value>321</value> <value>323</value> <value>325</value> <value>327</value> <value>329</value> <value>331</value> <value>333</value> <value>335</value> <value>337</value> <value>339</value> <value>341</value> <value>343</value> <value>345</value> <value>347</value> <value>349</value> <value>351</value> <value>353</value> <value>355</value> <value>357</value> <value>359</value> <value>361</value> <value>363</value> <value>365</value> <value>367</value> <value>369</value> <value>371</value> <value>373</value> <value>375</value> <value>377</value> <value>379</value>
        </Array>
        <Grid name="SST">
            <Array name="SST">
                <Attribute name="missing_value" type="Float32">
                    <value>-9.99999979e+33</value>
                </Attribute>
                <Attribute name="_FillValue" type="Float32">
                    <value>-9.99999979e+33</value>
                </Attribute>
                <Attribute name="long_name" type="String">
                    <value>SEA SURFACE TEMPERATURE</value>
                </Attribute>
                <Attribute name="history" type="String">
                    <value>From coads_climatology</value>
                </Attribute>
                <Attribute name="units" type="String">
                    <value>Deg C</value>
                </Attribute>
                <Float32/>
                <dimension name="TIME" size="12"/>
                <dimension name="COADSY" size="90"/>
                <dimension name="COADSX" size="180"/>
            </Array>
            <Map name="TIME">
                <Attribute name="units" type="String">
                    <value>hour since 0000-01-01 00:00:00</value>
                </Attribute>
                <Attribute name="time_origin" type="String">
                    <value>1-JAN-0000 00:00:00</value>
                </Attribute>
                <Attribute name="modulo" type="String">
                    <value> </value>
                </Attribute>
                <Float64/>
                <dimension name="TIME" size="12"/>
            </Map>
            <Map name="COADSY">
                <Attribute name="units" type="String">
                    <value>degrees_north</value>
                </Attribute>
                <Attribute name="point_spacing" type="String">
                    <value>even</value>
                </Attribute>
                <Float64/>
                <dimension name="COADSY" size="90"/>
            </Map>
            <Map name="COADSX">
                <Attribute name="units" type="String">
                    <value>degrees_east</value>
                </Attribute>
                <Attribute name="modulo" type="String">
                    <value> </value>
                </Attribute>
                <Attribute name="point_spacing" type="String">
                    <value>even</value>
                </Attribute>
                <Float64/>
                <dimension name="COADSX" size="180"/>
            </Map>
        </Grid>
        <Grid name="AIRT">
            <Array name="AIRT">
                <Attribute name="missing_value" type="Float32">
                    <value>-9.99999979e+33</value>
                </Attribute>
                <Attribute name="_FillValue" type="Float32">
                    <value>-9.99999979e+33</value>
                </Attribute>
                <Attribute name="long_name" type="String">
                    <value>AIR TEMPERATURE</value>
                </Attribute>
                <Attribute name="history" type="String">
                    <value>From coads_climatology</value>
                </Attribute>
                <Attribute name="units" type="String">
                    <value>DEG C</value>
                </Attribute>
                <Float32/>
                <dimension name="TIME" size="12"/>
                <dimension name="COADSY" size="90"/>
                <dimension name="COADSX" size="180"/>
            </Array>
            <Map name="TIME">
                <Attribute name="units" type="String">
                    <value>hour since 0000-01-01 00:00:00</value>
                </Attribute>
                <Attribute name="time_origin" type="String">
                    <value>1-JAN-0000 00:00:00</value>
                </Attribute>
                <Attribute name="modulo" type="String">
                    <value> </value>
                </Attribute>
                <Float64/>
                <dimension name="TIME" size="12"/>
            </Map>
            <Map name="COADSY">
                <Attribute name="units" type="String">
                    <value>degrees_north</value>
                </Attribute>
                <Attribute name="point_spacing" type="String">
                    <value>even</value>
                </Attribute>
                <Float64/>
                <dimension name="COADSY" size="90"/>
            </Map>
            <Map name="COADSX">
                <Attribute name="units" type="String">
                    <value>degrees_east</value>
                </Attribute>
                <Attribute name="modulo" type="String">
                    <value> </value>
                </Attribute>
                <Attribute name="point_spacing" type="String">
                    <value>even</value>
                </Attribute>
                <Float64/>
                <dimension name="COADSX" size="180"/>
            </Map>
        </Grid>
        <Grid name="UWND">
            <Array name="UWND">
                <Attribute name="missing_value" type="Float32">
                    <value>-9.99999979e+33</value>
                </Attribute>
                <Attribute name="_FillValue" type="Float32">
                    <value>-9.99999979e+33</value>
                </Attribute>
                <Attribute name="long_name" type="String">
                    <value>ZONAL WIND</value>
                </Attribute>
                <Attribute name="history" type="String">
                    <value>From coads_climatology</value>
                </Attribute>
                <Attribute name="units" type="String">
                    <value>M/S</value>
                </Attribute>
                <Float32/>
                <dimension name="TIME" size="12"/>
                <dimension name="COADSY" size="90"/>
                <dimension name="COADSX" size="180"/>
            </Array>
            <Map name="TIME">
                <Attribute name="units" type="String">
                    <value>hour since 0000-01-01 00:00:00</value>
                </Attribute>
                <Attribute name="time_origin" type="String">
                    <value>1-JAN-0000 00:00:00</value>
                </Attribute>
                <Attribute name="modulo" type="String">
                    <value> </value>
                </Attribute>
                <Float64/>
                <dimension name="TIME" size="12"/>
            </Map>
            <Map name="COADSY">
                <Attribute name="units" type="String">
                    <value>degrees_north</value>
                </Attribute>
                <Attribute name="point_spacing" type="String">
                    <value>even</value>
                </Attribute>
                <Float64/>
                <dimension name="COADSY" size="90"/>
            </Map>
            <Map name="COADSX">
                <Attribute name="units" type="String">
                    <value>degrees_east</value>
                </Attribute>
                <Attribute name="modulo" type="String">
                    <value> </value>
                </Attribute>
                <Attribute name="point_spacing" type="String">
                    <value>even</value>
                </Attribute>
                <Float64/>
                <dimension name="COADSX" size="180"/>
            </Map>
        </Grid>
        <Grid name="VWND">
            <Array name="VWND">
                <Attribute name="missing_value" type="Float32">
                    <value>-9.99999979e+33</value>
                </Attribute>
                <Attribute name="_FillValue" type="Float32">
                    <value>-9.99999979e+33</value>
                </Attribute>
                <Attribute name="long_name" type="String">
                    <value>MERIDIONAL WIND</value>
                </Attribute>
                <Attribute name="history" type="String">
                    <value>From coads_climatology</value>
                </Attribute>
                <Attribute name="units" type="String">
                    <value>M/S</value>
                </Attribute>
                <Float32/>
                <dimension name="TIME" size="12"/>
                <dimension name="COADSY" size="90"/>
                <dimension name="COADSX" size="180"/>
            </Array>
            <Map name="TIME">
                <Attribute name="units" type="String">
                    <value>hour since 0000-01-01 00:00:00</value>
                </Attribute>
                <Attribute name="time_origin" type="String">
                    <value>1-JAN-0000 00:00:00</value>
                </Attribute>
                <Attribute name="modulo" type="String">
                    <value> </value>
                </Attribute>
                <Float64/>
                <dimension name="TIME" size="12"/>
            </Map>
            <Map name="COADSY">
                <Attribute name="units" type="String">
                    <value>degrees_north</value>
                </Attribute>
                <Attribute name="point_spacing" type="String">
                    <value>even</value>
                </Attribute>
                <Float64/>
                <dimension name="COADSY" size="90"/>
            </Map>
            <Map name="COADSX">
                <Attribute name="units" type="String">
                    <value>degrees_east</value>
                </Attribute>
                <Attribute name="modulo" type="String">
                    <value> </value>
                </Attribute>
                <Attribute name="point_spacing" type="String">
                    <value>even</value>
                </Attribute>
                <Float64/>
                <dimension name="COADSX" size="180"/>
            </Map>
        </Grid>
    </Dataset>
</gar:GlacierArchiveRecord>
```

</font>