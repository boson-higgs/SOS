<h1>Linux a jeho instalace</h1>

<ul>
    <li>        
        GNU/Linux - operační systém (OS)
    </li>
    <li>
        OS = jádro + programy:
        <ul>
            <li>Linux - jádro OS (dodal Linus Torvalds)</li>
            <li>GNU - malé programy (ls, mkdir, ...) (dodal Richard Stallman)</li>
        </ul>
    </li>
    <li>
        Zdarma - pod licecní GNU/GPL-2.0
    </li>
    <li>
        Linux - Linusův Unix
    </li>
    <li>
        Jádro i programy bylo nutné přeložit a dát dohromady na vlasntím PC => vznik distribucí - společnosti, které předpřeložili jádro i malé programy a vydávají je jako jeden celek (instalační balíček - Debian, Ubuntu) - balíčkovací systémy, tyto společnosti vydělávají na podpoře
    </li>
    <li>
        Linux je majoritně nainstalován na serverech, minoritně na desktopech
    </li>
    <li>
        <a href="http://seidl.cs.vsb.cz/wiki2/index.php/SOS" target="_blanket">Instalace linuxu</a>
    </li>
    <li>
        veškerá konfigurace se provádí skrz soubory
    </li>
    <li>
        vše je proces nebo soubor (včetně připojených zařízení - disk, sériový port, ...)
    </li>
    <li>
        <h2>Struktura systému (složky)</h2>
        <ul>
            <li>
                ~ znamená symbolický link (variace na zástupce ve Windows) 
            </li>
            <li>
                daemon - program běžící na pozadí, nejde zabít, po ukončení tohoto programu se zapne znovu
            </li>
            <li>
                <table>
                    <tr>
                        <td>/bin</td>
                        <td>obsahuje binárky nejdůležitějších programů OS</td>
                    </tr>                
                    <tr>
                        <td>/boot</td>
                        <td>obsahuje jádro OS</td>
                    </tr>
                    <tr>
                        <td>/dev</td>
                        <td>prakticky neexistuje - vytváří jej jádro OS v operační paměti a nachází se v něm vše, co se týče HW/funkcí OS (sda, sda1, sdb, standartní vstup, standartní výstup, ...)</td>
                    </tr>
                    <tr>
                        <td>/etc</td>
                        <td>obsahuje většinu konfiguračních souborů OS</td>
                    </tr>
                    <tr>
                        <td>/home</td>
                        <td>obsahuje domovské adresáře všech uživatelů výjma roota</td>
                    </tr>
                    <tr>
                        <td>/lib<XX></td>
                        <td>obsahuje knihovny určené pro běh OS</td>
                    </tr>
                    <tr>
                        <td>lost+found</td>
                        <td>souvisí s formátováním disku</td>
                    </tr>
                    <tr>
                        <td>/media</td>
                        <td>do této složky se promítají vyměnitelná média (CDROM, flash disk, ...)</td>
                    </tr>
                    <tr>
                        <td>/mnt</td>
                        <td>historický význam - aktuálně se příliš nevyužívá, určena pro připojování disků za běhu OS (ke stejnému účelu se dá využít jakákoli jiná složka)</td>
                    </tr>
                    <tr>
                        <td>/opt</td>
                        <td>určená pro umístění softwaru třetích stran (software, který nesouvisí přímo s funkcí OS)</td>
                    </tr>
                    <tr>
                        <td>/proc</td>
                        <td>historický význam, vytváří se v operační paměti OS - OS zde promítá informace o svém běhu (běžících procesech, HW, ...)</td>
                    </tr>
                    <tr>
                        <td>/root</td>
                        <td>domovský adresář uživatele root</td>
                    </tr>
                    <tr>
                        <td>/run</td>
                        <td>obsahuje informace o běžících programech (daemoni si zde ukládají informace o svém běhu)</td>
                    </tr>
                    <tr>
                        <td>/sbin</td>
                        <td>obdoba složky bin</td>
                    </tr>
                    <tr>
                        <td>/srv</td>
                        <td>ne příliš využívána složka</td>
                    </tr>
                    <tr>
                        <td>/sys</td>
                        <td>moderní variant složky /proc</td>
                    </tr>
                    <tr>
                        <td>/tmp</td>
                        <td>osložka, do které si programy mohou odkládat své dočasné soubory</td>
                    </tr>
                    <tr>
                        <td>/usr</td>
                        <td>v této složce se nacházejí programy určené pro uživatele</td>
                    </tr>
                    <tr>
                        <td>/var</td>
                        <td>složka určená pro stacionární (měnící se) soubory (nachází zde složka log)<td>
                    </tr>
                    <tr>
                        <td>/var</td>
                        <td>složka určená pro stacionární (měnící se) soubory (nachází zde složka log)<td>
                    </tr>
                </table>
            </li>
        </ul>
    </li>
</ul>




